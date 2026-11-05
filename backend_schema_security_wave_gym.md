# Backend Schema & Security Document - Wave Gym (MVP)

Este documento define la arquitectura de datos, el flujo de autenticación, la organización de archivos multimedia y las reglas de seguridad en Firebase para la plataforma Wave Gym.

## 1. Flujo de Autenticación (Firebase Auth)

La identidad del usuario se gestiona centralmente mediante **Firebase Authentication**.

1. **Registro / Login:** El usuario se autentica vía Google Sign-In o Email/Contraseña.
2. **Generación de UID:** Firebase asigna un `uid` (User ID) único e irremplazable.
3. **Sincronización con Firestore:** Inmediatamente después del registro exitoso, la app crea un documento en la colección `users` de Firestore usando el mismo `uid` como ID del documento. Esto enlaza la autenticación con los datos del perfil y los roles.

## 2. Esquema de Base de Datos (Cloud Firestore)

Estructura NoSQL orientada a documentos. Se prioriza la lectura rápida y la separación de permisos.

### Colección: `users`
Almacena la información de perfil, roles y estado comercial.
* **Document ID:** `{uid}` (mismo que Firebase Auth)
* **Campos:**
  * `uid` (String)
  * `email` (String)
  * `full_name` (String)
  * `role` (String) -> `"master"`, `"trainer"`, `"student"`
  * `trainer_id` (String, nullable) -> Obligatorio si el rol es `"student"`.
  * `trial_end_date` (Timestamp, nullable) -> Solo para Trainers.
  * `subscription_status` (String) -> `"active"`, `"inactive"`, `"trialing"`.
  * `is_payment_due` (Boolean) -> Control manual del Trainer sobre el Student.

### Colección: `exercises_library`
Base de datos de ejercicios. Se separan los globales de los privados mediante el ID del creador.
* **Document ID:** Auto-generado
* **Campos:**
  * `name` (String)
  * `video_url` (String) -> URL del archivo en Firebase Storage.
  * `trainer_owner_id` (String) -> `uid` del entrenador creador, o `"GLOBAL"` si fue creado por el Master.
  * `created_at` (Timestamp)

### Colección: `workouts`
Rutinas asignadas a los alumnos.
* **Document ID:** Auto-generado
* **Campos:**
  * `student_id` (String) -> `uid` del alumno dueño de la rutina.
  * `trainer_id` (String) -> `uid` del entrenador que la asignó.
  * `title` (String) -> Ej: "Día 1: Full Body".
  * `blocks` (Array de Objetos) -> Estructura:
    * `exercise_id` (String - ref a exercises_library)
    * `series` (Number)
    * `reps` (String) -> Ej: "10-12"
    * `rest_time_seconds` (Number)
    * `suggested_weight` (String)
    * `is_superset` (Boolean)

### Colección: `progress_logs`
Métricas y evolución de los alumnos.
* **Document ID:** Auto-generado
* **Campos:**
  * `student_id` (String)
  * `date` (Timestamp)
  * `body_weight` (Number)
  * `photos` (Array de Strings) -> URLs de las fotos en Firebase Storage.
  * `feedback_notes` (String)

## 3. Organización de Archivos (Firebase Storage)

Los archivos multimedia (videos de ejercicios, fotos de progreso) se organizan en carpetas estrictas para aplicar reglas de seguridad de forma eficiente y evitar colisiones de nombres.

* `global/`
  * `videos/` -> Videos creados por el admin Master. (Ej: `global/videos/sentadilla_basica.mp4`)
* `trainers/`
  * `{trainer_uid}/`
    * `videos/` -> Base de datos privada de videos del entrenador.
* `students/`
  * `{student_uid}/`
    * `progress_photos/` -> Fotos subidas en los reportes de evolución.

## 4. Capas de Seguridad (Firebase Security Rules)

El corazón de la privacidad de Wave Gym. Estas reglas aseguran que nadie vea datos que no le corresponden, evaluando el token de Auth y los datos del documento.

### Reglas de Firestore (`firestore.rules`)
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Función auxiliar para leer el rol
    function getRole() {
      return get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role;
    }
    
    // Colección Users
    match /users/{userId} {
      // Un usuario puede leer y escribir su propio perfil
      allow read, write: if request.auth.uid == userId;
      // Un entrenador puede leer perfiles de usuarios que lo tengan como trainer_id
      allow read: if getRole() == 'trainer' && resource.data.trainer_id == request.auth.uid;
      // El master tiene acceso global
      allow read, write: if getRole() == 'master';
    }

    // Colección Exercises Library
    match /exercises_library/{exerciseId} {
      // Todos pueden leer si es GLOBAL. Alumnos y trainers pueden leer si el trainer_owner_id coincide con el suyo.
      allow read: if resource.data.trainer_owner_id == 'GLOBAL' 
                  || resource.data.trainer_owner_id == request.auth.uid 
                  || resource.data.trainer_owner_id == get(/databases/$(database)/documents/users/$(request.auth.uid)).data.trainer_id;
      // Solo el dueño puede editar/borrar
      allow write: if resource.data.trainer_owner_id == request.auth.uid || getRole() == 'master';
    }

    // Colección Workouts y Progress
    match /workouts/{workoutId} {
      allow read: if resource.data.student_id == request.auth.uid || resource.data.trainer_id == request.auth.uid;
      allow write: if resource.data.trainer_id == request.auth.uid;
    }
    
    match /progress_logs/{logId} {
      allow read: if resource.data.student_id == request.auth.uid || get(/databases/$(database)/documents/users/$(resource.data.student_id)).data.trainer_id == request.auth.uid;
      allow write: if resource.data.student_id == request.auth.uid;
    }
  }
}
```

### Reglas de Storage (`storage.rules`)
```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    
    // Videos Globales: Solo el master escribe, todos leen.
    match /global/videos/{videoId} {
      allow read: if request.auth != null;
      allow write: if firestore.get(/databases/(default)/documents/users/$(request.auth.uid)).data.role == 'master';
    }

    // Videos de Entrenadores: Solo el entrenador escribe. Leen el entrenador y sus alumnos vinculados.
    match /trainers/{trainerId}/videos/{videoId} {
      allow read: if request.auth.uid == trainerId || firestore.get(/databases/(default)/documents/users/$(request.auth.uid)).data.trainer_id == trainerId;
      allow write: if request.auth.uid == trainerId;
    }

    // Fotos de Evolución: Escribe el alumno. Lee el alumno y su entrenador.
    match /students/{studentId}/progress_photos/{photoId} {
      allow write: if request.auth.uid == studentId;
      allow read: if request.auth.uid == studentId || firestore.get(/databases/(default)/documents/users/$(studentId)).data.trainer_id == request.auth.uid;
    }
  }
}
```