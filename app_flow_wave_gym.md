# Documento de Flujo de Usuario y Navegación - Wave Gym (MVP)

Este documento detalla el mapa de navegación general de la aplicación y el comportamiento exacto (User Journey) al interactuar con los elementos de la interfaz. La navegación principal estará gestionada por un enrutador declarativo (GoRouter) que evaluará el estado de autenticación y el rol del usuario para redirigirlo a la rama correspondiente.

---

## 1. Árbol de Navegación Global (Router Tree)

*   **`/splash`** (Pantalla de carga inicial)
*   **`/auth`** (Login / Registro)
    *   **`/auth/onboarding-student`** (Pantalla para ingresar el ID del entrenador)
*   **`/paywall`** (Pantalla de suscripción para Entrenadores con Trial vencido)
*   **`/trainer`** (Rama exclusiva para Entrenadores)
    *   `/trainer/home` (Dashboard)
    *   `/trainer/library` (Librería multimedia)
    *   `/trainer/create-workout` (Constructor de rutinas)
*   **`/student`** (Rama exclusiva para Alumnos)
    *   `/student/home` (Dashboard)
    *   `/student/workout-player` (Motor inmersivo de entrenamiento)
    *   `/student/progress` (Evolución y formularios)
*   **`/master`** (Rama exclusiva para Admin global)

---

## 2. Flujo de Autenticación y Onboarding

**Pantalla Inicial: `/auth`**
*   **Vista:** Formulario Neumórfico con inputs de Email/Contraseña y botón de Google.
*   **Acción > "Ingresar con Google" (o "Registrarse"):**
    *   *Si es un usuario nuevo:* Se abre un modal (Bottom Sheet) preguntando: "¿Eres Entrenador o Alumno?".
        *   Si elige **Entrenador**: Se crea el perfil, se inicia el trial de 7 días y navega a `/trainer/home`.
        *   Si elige **Alumno**: Navega a `/auth/onboarding-student`.
    *   *Si es un usuario existente:* El sistema verifica su rol y redirige.

**Pantalla de Onboarding Alumno: `/auth/onboarding-student`**
*   **Vista:** Input central "Ingresa el ID de tu Entrenador".
*   **Acción > Botón "Vincular":**
    *   El sistema valida el ID en Firestore.
    *   *Si es correcto:* Guarda la vinculación y navega a `/student/home`.
    *   *Si es incorrecto:* Muestra un mensaje de error en rojo y mantiene al usuario en la misma pantalla.

**Flujo del Paywall (Solo Entrenadores): `/paywall`**
*   *Condición de aparición:* Se muestra automáticamente al abrir la app si `trial_end_date` expiró y `subscription_status` es "inactive".
*   **Vista:** Tarjetas de planes (Mensual/Anual), Input para "Cupón de Descuento", Botón "Pagar con Stripe".
*   **Acción > Botón "Pagar":** Llama a la pasarela de Stripe. Si es exitoso, navega a `/trainer/home`.

---

## 3. Flujo del Entrenador (Trainer Journey)

Una vez en la rama `/trainer`, la navegación inferior (Bottom Navigation Bar) tiene 2 pestañas principales: **Inicio** y **Librería**.

### 3.1. Tab Inicio: `/trainer/home`
*   **Vista:** Lista de alumnos activos y métricas rápidas.
*   **Acción > Tap en el Badge "ID de Entrenador" (Header):**
    *   Copia el ID al portapapeles y muestra un *Snack Bar* (mensaje inferior): "ID copiado".
*   **Acción > Toggle Switch de Pago (por Alumno):**
    *   *Si apaga el switch (Gris/Hundido):* El alumno se marca como "Pago Pendiente". El cronjob del servidor le enviará una notificación Push de cobro.
    *   *Si enciende el switch (Rojo/Saliente):* El alumno está "Al día". No recibe alertas.
*   **Acción > Botón FAB Flotante (+):**
    *   Abre un menú con dos opciones: "Agregar Alumno" y "Crear Rutina".
*   **Acción > "Crear Rutina":**
    *   Navega a `/trainer/create-workout` donde se arma la lista de ejercicios, se marcan las superseries (checkbox) y se guardan en la DB. Al presionar "Guardar Rutina", regresa a `/trainer/home`.

### 3.2. Tab Librería: `/trainer/library`
*   **Vista:** Cuadrícula de videos propios subidos por el entrenador.
*   **Acción > Botón "Subir Video":**
    *   Abre la galería del teléfono (o cámara), sube el archivo a Firebase Storage y lo lista en la pantalla.

---

## 4. Flujo del Alumno (Student Journey)

El alumno tiene 3 pestañas en la barra inferior: **Inicio**, **Evolución** y **Ajustes**.

### 4.1. Tab Inicio: `/student/home`
*   **Vista:** Tarjeta de la rutina del día asignada por su entrenador.
*   **Acción > Botón "INICIAR ENTRENAMIENTO":**
    *   Navega instantáneamente a `/student/workout-player` (Inicia el motor de entrenamiento).

### 4.2. El Motor de Entrenamiento: `/student/workout-player`
Esta pantalla funciona como una **Máquina de Estados** (State Machine) que avanza de forma lineal.

*   **Estado A: Vista del Ejercicio**
    *   Muestra el video en loop, el peso, las repeticiones y la técnica.
    *   **Acción > Botón "MARCAR COMPLETADO":**
        *   *Evaluación interna:* ¿Este ejercicio forma parte de una Superserie?
            *   *SÍ:* Transición directa al "Estado A" del siguiente ejercicio (sin cronómetro).
            *   *NO:* Transición al "Estado B" (Descanso).

*   **Estado B: Pantalla de Descanso (Resting State)**
    *   Muestra un cronómetro gigante en cuenta regresiva (ej. 01:30 -> 01:29).
    *   **Acción > Botón "+30 Seg":**
        *   Suma 30 segundos al reloj actual sin pausarlo.
    *   **Acción > Botón "Saltar Descanso":**
        *   Finaliza el cronómetro abruptamente y hace transición al "Estado A" del siguiente ejercicio.
    *   *Si el cronómetro llega a 00:00:* Vibra el teléfono y transiciona automáticamente al "Estado A" del siguiente ejercicio.

*   **Fin de la Rutina:**
    *   Al completar el último ejercicio de la lista, navega a una pantalla de felicitaciones: "¡Entrenamiento Completado!".
    *   **Acción > Botón "Volver al Inicio":** Retorna a `/student/home` y actualiza la barra de progreso semanal.

### 4.3. Tab Evolución: `/student/progress`
*   **Vista:** Gráfica lineal del peso corporal y cuadrícula de fotos.
*   **Acción > Botón "Añadir Métricas de Hoy":**
    *   Levanta un modal (Efecto Glassmorphism sobre la pantalla actual).
    *   Permite cargar: Peso actual (Input numérico) y 3 Fotos (Frente, Perfil, Espalda).
    *   **Acción > Botón "Guardar Progreso":** Sube las fotos y datos a Firebase, cierra el modal y recarga la gráfica de la pantalla.