# Technical Requirements Document (TRD) - Wave Gym (MVP)

## 1. Stack Tecnológico y Dependencias (Flutter)
Para mantener un entorno de desarrollo escalable y cumplir con el patrón MVVM, utilizaremos las siguientes herramientas y paquetes principales en `pubspec.yaml`:

* **Gestor de Estado (ViewModel):** `flutter_riverpod` (ideal para inyectar dependencias y manejar el estado reactivo en MVVM) o `provider`.
* **Modelos Inmutables:** `freezed` y `json_serializable` (para generar el código boilerplate de los modelos de datos y facilitar la conversión hacia/desde Firestore).
* **Navegación:** `go_router` (manejo de rutas declarativo, ideal para gestionar la redirección al *Paywall* o el flujo de bloqueo si falta el ID de entrenador).
* **Backend (Firebase):** `firebase_core`, `firebase_auth`, `cloud_firestore`, `firebase_storage`, `firebase_messaging` (para notificaciones push).
* **Pagos:** `flutter_stripe` (SDK oficial de Stripe para procesar pagos en la app móvil).
* **Autenticación Social:** `google_sign_in`.
* **Multimedia:** `video_player` (para reproducir los ejercicios en la app interactiva), `image_picker` (para las fotos de evolución).

## 2. Estructura de Directorios (Arquitectura MVVM)
La carpeta `lib/` del proyecto Flutter se organizará enfocada en las funcionalidades (Feature-first) para mayor escalabilidad:

```text
lib/
 ├── core/                 # Configuraciones globales (Tema, colores Rise Wave Tech, utilidades)
 ├── data/
 │    ├── models/          # Modelos de datos inmutables (Freezed)
 │    ├── repositories/    # Lógica de conexión a Firebase (Firestore, Storage, Auth)
 ├── features/
 │    ├── auth/            # Vistas y ViewModels de Registro, Login y Paywall
 │    ├── dashboard/       # Paneles principales (Trainer y Student)
 │    ├── workouts/        # Lógica de ejecución del entrenamiento interactivo y cronómetros
 │    ├── progress/        # Evolución, fotos y métricas
 │    └── library/         # Gestión de la base de datos de ejercicios (privada y global)
 └── main.dart             # Punto de entrada e inicialización de Firebase/Riverpod
```

## 3. Esquema de Base de Datos (Cloud Firestore)
Dado que usaremos una base de datos NoSQL, estructuraremos las colecciones para minimizar las lecturas y optimizar costos:

* **Colección `users`**
  * `uid` (String): ID de Firebase Auth.
  * `role` (String): "master", "trainer", o "student".
  * `trainer_id` (String, nullable): ID del entrenador al que está vinculado (obligatorio para students).
  * `trial_end_date` (Timestamp): Fecha en la que caduca la prueba de 7 días (para trainers).
  * `subscription_status` (String): "active", "inactive", "trialing".
  * `is_payment_due` (Boolean): Switch manual que maneja el trainer para recordar pagos a sus alumnos por fuera de la app.

* **Colección `workouts`** (Subcolección recomendada dentro de cada alumno o referenciada por ID)
  * `workout_id` (String).
  * `student_id` (String).
  * `blocks` (Array de Mapas): Cada mapa contendrá: `{ exercise_id, series, reps, rest_time_seconds, suggested_weight, is_superset }`.

* **Colección `exercises_library`**
  * `exercise_id` (String).
  * `name` (String).
  * `video_url` (String).
  * `trainer_owner_id` (String): Si es "GLOBAL", es del Master y lo ven todos. Si tiene el ID de un trainer, solo ese trainer puede leerlo.

* **Colección `coupons`**
  * `code` (String): Ejemplo "WAVE50".
  * `discount_percentage` (Number).
  * `valid_until` (Timestamp).

## 4. Lógica de Backend (Firebase Cloud Functions - Node.js)
El código frontend no debe procesar pagos críticos por seguridad. Necesitaremos habilitar el plan Blaze (pago por uso, con capa gratuita) y desplegar las siguientes funciones en Node.js:
1. `createStripeCustomer`: Se dispara cuando se crea un usuario con rol `trainer` en Firebase Auth.
2. `processSubscription`: Recibe el token de pago desde Flutter, valida si hay un cupón en Firestore, y ejecuta el cargo en Stripe. Actualiza el `subscription_status` del trainer a "active".
3. `checkStudentPayments`: Un *Cron Job* (Scheduled Function) que corre una vez al día. Busca en la colección `users` todos los students que tengan `is_payment_due == true` y dispara una notificación Push (FCM) a sus dispositivos.

## 5. Reglas de Seguridad (Firebase Security Rules)
Para garantizar el aislamiento de la librería multimedia y proteger los datos:

* **Firestore (`exercises_library`):** Un entrenador solo puede hacer lectura/escritura si `resource.data.trainer_owner_id == request.auth.uid`. Todos pueden leer si `trainer_owner_id == "GLOBAL"`.
* **Storage (Videos):** Los videos se guardarán en rutas como `trainers/{uid}/videos/`. La regla validará que el `uid` de la ruta coincida con el usuario autenticado. El admin maestro podrá escribir en la ruta `global/videos/`.
* **Lectura de Alumnos:** Un `trainer` solo puede leer los documentos de progreso o perfiles de usuarios si `resource.data.trainer_id == request.auth.uid`.

## 6. Lógica de Ejecución (Workout Engine)
En la vista interactiva (MVVM - `WorkoutViewModel`), la app manejará el estado a través de un índice (`currentExerciseIndex`). 
* **Transiciones:** Si `is_superset == true`, el ViewModel incrementa el índice instantáneamente al siguiente ejercicio al marcar "Completado".
* **Cronómetro:** Si `is_superset == false` (o termina el bloque de la superserie), el estado cambia a `RestingState`, activando un temporizador local. Se usarán `Streams` o `Tickers` en Flutter para manejar la cuenta regresiva sin congelar la UI, inyectando métodos para sumar 30s o saltar el descanso.