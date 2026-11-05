# Software Design Document (SDD) - Wave Gym (MVP)

## 1. Visión General del Producto
**Wave Gym** es una aplicación móvil (iOS y Android) desarrollada bajo el ecosistema de **Rise Wave Tech**. Está diseñada como una plataforma B2B2C (SaaS) donde los entrenadores personales gestionan a sus alumnos, planifican rutinas, hacen seguimiento de evolución y controlan el estado de pagos. Los alumnos utilizan la app para ejecutar sus rutinas de forma interactiva y registrar su progreso.

## 2. Interfaz y Experiencia de Usuario (UX/UI)
El diseño visual reflejará la identidad de Rise Wave Tech, implementando un estilo moderno que aprovecha el *glassmorphism* para destacar elementos sobre los fondos.
*   **Plataforma:** Exclusiva para dispositivos móviles (Flutter).
*   **Paleta de Colores (Variables Globales):**
    *   Primario: `#D93025` (Wave Gym Red)
    *   Hover/Accento: `#B3241B`
    *   Fondo: `#F4F1EA` (Ice White/Crema)
    *   Texto: `#1A1A1A` (Carbon Black)
    *   Glass Background: `rgba(255, 255, 255, 0.25)`
    *   Glass Border: `rgba(255, 255, 255, 0.5)`
*   **Tipografía:** Inter (cuerpo y lectura clara) y Orbitron (títulos, cronómetros y acentos tecnológicos).

## 3. Arquitectura y Stack Tecnológico
*   **Frontend:** Flutter (Patrón MVVM - Model-View-ViewModel).
*   **Backend as a Service (BaaS):** Firebase.
    *   *Auth:* Email/Password y Google Sign-In.
    *   *Firestore:* Base de datos NoSQL para usuarios, rutinas, logs y cupones.
    *   *Storage:* Almacenamiento de fotos de evolución y videoteca de ejercicios.
    *   *Cloud Messaging (FCM):* Orquestación de notificaciones Push.

## 4. Especificación de Módulos (Fase 1 - MVP)

### 4.1. Autenticación y Roles de Usuario
*   **Roles:** `Master` (Admin global), `Trainer` (Entrenador de pago) y `Student` (Alumno gratuito).
*   **Métodos:** Google Sign-In (OAuth) y Registro tradicional (Email/Contraseña).
*   **Flujo de Registro:** El formulario por email requerirá campos completos (Nombre, Apellido, Fecha de Nacimiento, Género, etc.).

### 4.2. Gestión de Clientes (Vinculación)
*   Todo perfil `Trainer` tendrá un `trainer_id` único, visible en su panel principal.
*   El `Trainer` puede crear cuentas de `Student` desde su panel (vinculación automática).
*   Si un `Student` se registra de forma autónoma, el flujo de *onboarding* bloqueará el uso de la app hasta que ingrese un `trainer_id` válido para quedar asociado en la base de datos.

### 4.3. Suscripciones y Pagos (SaaS Entrenadores)
*   **Pasarela:** Stripe (Tarjetas de crédito/débito).
*   **Lógica de Acceso (`Trainer`):**
    *   Al registrarse, obtienen **7 días de prueba gratuita** de forma automática (gestionado en Firestore comparando la fecha de registro).
    *   Finalizado el trial, se levanta el *Paywall* exigiendo la suscripción.
*   **Sistema de Cupones:** El usuario `Master` tendrá una vista exclusiva para generar códigos alfanuméricos. Estos cupones aplicarán descuentos sobre los planes (mensual, semestral, anual o vitalicio). El *Paywall* incluirá el campo "Ingresar cupón".
*   **Control de Alumnos:** El `Trainer` tendrá un panel con un *checkbox* o interruptor por cada alumno para marcar quién abonó su servicio por fuera de la app. 

### 4.4. Notificaciones y Recordatorios
*   El sistema evaluará diariamente el estado de pagos de los alumnos.
*   Para aquellos que el `Trainer` no haya marcado como "Pagado", se disparará una **Notificación Push** al dispositivo del `Student` recordando el pago.
*   Esta notificación quedará registrada en una vista de "Centro de Notificaciones" (campanita) dentro de la interfaz principal de la app.

### 4.5. Sistema de Entrenamiento y Ejecución Interactiva
Este es el motor *core* de la experiencia de usuario. 

**Estructura de Datos del Ejercicio:**
*   Nombre del ejercicio.
*   Series y Repeticiones.
*   Tiempo de descanso (sugerido por el entrenador).
*   Peso sugerido.
*   Notas de técnica / Instrucciones.
*   *Flag* de Superserie (Booleano a nivel ejercicio o bloque completo).

**Modo de Ejecución (Student UX):**
La ejecución se modela como una máquina de estados inmersiva. Al presionar "Iniciar Entrenamiento", el alumno entra en una vista paso a paso:
1.  **Vista de Ejercicio:** Muestra el video del ejercicio, series, reps, peso y técnica.
2.  **Acción:** El usuario realiza la serie y presiona el botón "Completado".
3.  **Transición de Descanso:** Automáticamente la vista cambia a un cronómetro de cuenta regresiva (basado en el tiempo sugerido). 
4.  **Controles de Descanso:** En pantalla aparecerán dos botones: `+30 Segundos` (para añadir tiempo) y `Saltar Descanso / Siguiente` (para omitir el cronómetro y pasar directo a la próxima serie o ejercicio).
5.  **Superseries:** Si los ejercicios A y B están en superserie, el botón "Completado" del Ejercicio A pasa directamente a la vista del Ejercicio B sin disparar el cronómetro, hasta terminar el ciclo.

### 4.6. Seguimiento, Evolución y Librería
*   **Evolución:** Módulo para cargar métricas corporales (peso, medidas), fotografías de progreso y envío de formularios semanales de feedback.
*   **Librería Aislada (`Trainer`):** Cada entrenador tendrá su propia colección en Firestore/Storage para subir videos (ejercicios propios), los cuales serán inaccesibles para otros entrenadores mediante Reglas de Seguridad de Firebase.
*   **Librería Global (`Master`):** El administrador podrá cargar videos genéricos que estarán disponibles en la biblioteca de todos los entrenadores.