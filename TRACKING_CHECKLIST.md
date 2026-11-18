# Wave Gym - Tracking Checklist & Execution Guide

> **Fuente:** `plan_de_implementaci_n_wave_gym.md` + `wave_gym_software_design_document.md`
> **Última actualización:** 2026-11-17
> **Propósito:** Checklist ejecutable por cualquier agente IA para dar seguimiento al proyecto Wave Gym (MVP)

---

## 📌 Información Base del Proyecto

| Campo | Valor |
|-------|-------|
| **Proyecto** | Wave Gym (MVP) |
| **Ecosistema** | Rise Wave Tech |
| **Plataforma** | iOS + Android (Flutter) |
| **Arquitectura** | MVVM (Model-View-ViewModel) |
| **Backend** | Firebase (Auth, Firestore, Storage, FCM, Cloud Functions) |
| **Pagos** | Stripe |
| **Notificaciones** | FCM + Cron Jobs diarios |
| **Repositorio** | `C:\Users\Luis\Documents\dev\wave-gym` |

### Paleta de Colores (Design System)
```yaml
colors:
  primary: "#D93025"           # Wave Gym Red
  primary_hover: "#B3241B"     # Hover/Accento
  background: "#F4F1EA"        # Ice White/Crema
  text: "#1A1A1A"              # Carbon Black
  glass_bg: "rgba(255,255,255,0.25)"
  glass_border: "rgba(255,255,255,0.5)"
typography:
  body: "Inter"
  display: "Orbitron"          # Títulos, cronómetros, acentos tech
```

### Roles de Usuario
| Role | Descripción | Acceso |
|------|-------------|--------|
| `Master` | Admin global | Panel cupones, librería global, métricas plataforma |
| `Trainer` | Entrenador de pago | Gestión alumnos, rutinas, librería propia, pagos externos |
| `Student` | Alumno gratuito | Ejecución rutinas, evolución, notificaciones |

---

## ✅ FASE 1: Setup Core y Arquitectura Base (Semana 1)

### 1.1 Inicialización Proyecto Flutter
- [ ] **Acción:** Crear proyecto Flutter con estructura MVVM
- [ ] **Agente:** `engineering-software-architect` + `engineering-mobile-app-builder`
- [ ] **Comandos esperados:**
  ```bash
  flutter create wave_gym --org com.risewavetech --platforms=ios,android
  cd wave_gym
  ```
- [ ] **Estructura de carpetas a crear:**
  ```
  lib/
  ├── core/
  │   ├── theme/           # ThemeData, colores, tipografías
  │   ├── constants/       # App constants, keys
  │   ├── utils/           # Helpers, extensions
  │   └── di/              # Dependency injection (get_it / riverpod)
  ├── data/
  │   ├── models/          # Entidades + fromJson/toJson
  │   ├── repositories/    # Implementaciones de repositorios
  │   └── datasources/     # Firebase, Stripe, Local storage
  ├── domain/
  │   ├── entities/        # Entidades puras (sin dependencias)
  │   ├── repositories/    # Interfaces/abstracts de repositorios
  │   └── usecases/        # Casos de uso (single responsibility)
  ├── presentation/
  │   ├── viewmodels/      # ViewModels (ChangeNotifier / Riverpod)
  │   ├── views/           # Pantallas (Widgets stateless/stateful)
  │   ├── widgets/         # Componentes reutilizables
  │   └── routes/          # GoRouter / AutoRoute config
  └── main.dart
  ```
- [ ] **Dependencias `pubspec.yaml` (mínimas):**
  ```yaml
  dependencies:
    flutter: sdk: flutter
    firebase_core: ^3.0.0
    firebase_auth: ^5.0.0
    cloud_firestore: ^5.0.0
    firebase_storage: ^12.0.0
    firebase_messaging: ^15.0.0
    cloud_functions: ^5.0.0
    google_sign_in: ^6.2.0
    stripe_payment: ^1.1.0  # o flutter_stripe
    go_router: ^14.0.0
    provider: ^6.1.0 / riverpod: ^2.5.0
    freezed_annotation: ^2.4.0
    json_annotation: ^4.9.0
    equatable: ^2.0.5
    intl: ^0.19.0
    image_picker: ^1.1.0
    video_player: ^2.9.0
    cached_network_image: ^3.3.0
    flutter_local_notifications: ^17.0.0
    permission_handler: ^11.0.0
    connectivity_plus: ^6.0.0
    hive: ^2.2.0          # Cache local offline-first
    hive_flutter: ^1.1.0
  dev_dependencies:
    build_runner: ^2.4.0
    freezed: ^2.5.0
    json_serializable: ^6.8.0
    flutter_lints: ^5.0.0
    integration_test: sdk: flutter
  ```
- [ ] **Verificación:** `flutter analyze` sin errores + `flutter test` pasa

---

### 1.2 Setup Firebase (Auth, Firestore, Storage, FCM, Functions)
- [ ] **Acción:** Crear proyecto Firebase y configurar apps iOS/Android
- [ ] **Agente:** `engineering-backend-architect`
- [ ] **Pasos:**
  1. `firebase login` → `firebase projects:create wave-gym-prod` (y `wave-gym-dev`)
  2. `flutterfire configure` → genera `firebase_options.dart` para ambos flavors
  3. Habilitar en Console:
     - **Authentication:** Email/Password + Google Sign-In
     - **Firestore:** Base de datos en modo producción (reglas después)
     - **Storage:** Bucket para videos + fotos evolución
     - **Cloud Messaging:** Configurar APNs (iOS) + FCM (Android)
     - **Cloud Functions:** Node.js 20, región `us-central1` o `europe-west1`
- [ ] **Archivos generados:** `lib/firebase_options.dart`, `google-services.json` (android), `GoogleService-Info.plist` (ios)
- [ ] **Verificación:** `flutter run` conecta a Firebase sin errores

---

### 1.3 CI/CD Básico (GitHub Actions / Codemagic)
- [ ] **Acción:** Pipeline que compile, testee y genere artifacts
- [ ] **Agente:** `engineering-devops-automator`
- [ ] **Archivo:** `.github/workflows/ci.yml`
  ```yaml
  name: CI
  on: [push, pull_request]
  jobs:
    build:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v4
        - uses: subosito/flutter-action@v2
          with: { flutter-version: '3.24', channel: 'stable' }
        - run: flutter pub get
        - run: flutter analyze
        - run: flutter test --coverage
        - run: flutter build apk --debug
        - run: flutter build ios --debug --no-codesign
  ```
- [ ] **Verificación:** PR ejecuta workflow y pasa

---

## ✅ FASE 2: Diseño UI y Componentes Base (Semana 2)

### 2.1 Tema Global (`core/theme/`)
- [ ] **Acción:** Implementar `AppTheme` con colores Rise Wave + Glassmorphism
- [ ] **Agente:** `design-ui-designer` (tokens) + `engineering-frontend-developer` (código)
- [ ] **Archivos:**
  - `lib/core/theme/app_colors.dart` — constants de paleta
  - `lib/core/theme/app_text_styles.dart` — Inter + Orbitron
  - `lib/core/theme/app_theme.dart` — `ThemeData` light/dark
  - `lib/core/theme/glassmorphism.dart` — widgets `GlassContainer`, `GlassButton`
- [ ] **Validación visual:** `testing-reality-checker` compara contra wireframes/Figma

---

### 2.2 Componentes Reutilizables (`presentation/widgets/`)
- [ ] **Acción:** Crear kit de componentes base
- [ ] **Agente:** `engineering-frontend-developer`
- [ ] **Componentes obligatorios:**
  | Widget | Descripción | Props clave |
  |--------|-------------|-------------|
  | `NeumorphicButton` | Botón principal estilo neumórfico | `onPressed`, `child`, `enabled`, `variant` |
  | `GlassCard` | Tarjeta con glassmorphism | `child`, `padding`, `borderRadius` |
  | `AppTextField` | Input con validación, label, error | `controller`, `validator`, `keyboardType` |
  | `VideoPlayerWidget` | Reproductor ejercicios (cached + fullscreen) | `videoUrl`, `autoPlay`, `loop` |
  | `CountdownTimer` | Cronómetro regresivo (descansos) | `duration`, `onTick`, `onComplete`, `controls` |
  | `AvatarWidget` | Foto perfil con fallback inicial | `imageUrl`, `initials`, `radius` |
  | `BottomSheetModal` | Modal consistente app | `title`, `child`, `actions` |
  | `LoadingOverlay` | Overlay carga global | `isLoading`, `message` |
  | `EmptyState` | Pantalla vacía con ilustración + acción | `icon`, `title`, `subtitle`, `action` |
  | `NotificationBadge` | Badge campanita con contador | `count`, `onTap` |
- [ ] **Tests:** Unit tests de render + golden tests (opcional)
- [ ] **Validación:** `testing-reality-checker` aprueba consistencia visual

---

## ✅ FASE 3: Features Cliente - Lógica de Negocio (Semanas 3-4)

### 3.1 Autenticación y Onboarding
- [ ] **Acción:** Implementar flujo completo Auth + Onboarding
- [ ] **Agentes:** `design-ux-architect` (flujos) + `engineering-mobile-app-builder`
- [ ] **Pantallas/ViewModels:**
  - `SplashScreen` + `SplashViewModel` (check auth state, navigate)
  - `LoginScreen` + `LoginViewModel` (email/pass + Google)
  - `RegisterScreen` + `RegisterViewModel` (campos completos: nombre, apellido, DOB, género)
  - `OnboardingScreen` + `OnboardingViewModel` (trainer_id input para Students)
  - `ForgotPasswordScreen` + `ResetPasswordScreen`
- [ ] **Lógica clave:**
  - `AuthRepository` interface + `FirebaseAuthRepository` impl
  - `AuthUseCases`: `signIn`, `signUp`, `signInWithGoogle`, `sendPasswordReset`, `signOut`, `getCurrentUser`
  - `OnboardingUseCase`: `completeOnboarding(trainerId?)` → valida trainer existe en Firestore
  - Persistencia sesión: `User` entity en `SharedPreferences` / `Riverpod` state
- [ ] **Validación:** `testing-workflow-optimizer` genera unit tests ViewModels (cobertura ≥70%)

---

### 3.2 Dashboards (Trainer + Student)
- [ ] **Acción:** Pantallas principales post-login según rol
- [ ] **Agentes:** `design-ux-architect` + `engineering-mobile-app-builder`

#### Trainer Dashboard
- [ ] `TrainerDashboardScreen` + `TrainerDashboardViewModel`
- [ ] **Widgets:** `StudentListTile` (nombre, estado pago, toggle "pagado externo"), `QuickActions` (crear rutina, ver pagos, cupón)
- [ ] **Estado:** Stream `students` (where `trainerId == currentUser.uid`)
- [ ] **Acciones:** Toggle `isPaidExternally` → actualiza Firestore + dispara FCM si cambia a false

#### Student Dashboard
- [ ] `StudentDashboardScreen` + `StudentDashboardViewModel`
- [ ] **Widgets:** `ActiveRoutineCard` (próxima rutina, botón "Iniciar"), `EvolutionSummary`, `NotificationBell`
- [ ] **Estado:** `currentRoutine` (última asignada), `notifications` (subcolección usuario)

---

### 3.3 Motor Inmersivo de Entrenamiento (CORE)
- [ ] **Acción:** Máquina de estados para ejecución de rutinas
- [ ] **Agentes:** `design-ux-architect` (validar UX) + `engineering-mobile-app-builder`
- [ ] **Entidades de dominio:**
  ```dart
  // domain/entities/workout.dart
  @freezed
  class Exercise with _$Exercise {
    const factory Exercise({
      required String id,
      required String name,
      required int sets,
      required int reps,
      required int restSeconds,
      required double? suggestedWeight,
      required String? techniqueNotes,
      required String? videoUrl,
      required bool isSuperset,        // true si encadena con siguiente
      required int order,
    }) = _Exercise;
  }

  @freezed
  class Routine with _$Routine {
    const factory Routine({
      required String id,
      required String trainerId,
      required String studentId,
      required String name,
      required List<Exercise> exercises,
      required DateTime createdAt,
      required bool isActive,
    }) = _Routine;
  }
  ```
- [ ] **Estados de la máquina (`WorkoutState`):**
  | Estado | Descripción | Transiciones |
  |--------|-------------|--------------|
  | `WorkoutInitial` | Pantalla "Iniciar Entrenamiento" | → `ExerciseView` |
  | `ExerciseView` | Ver video, series, reps, peso, técnica | `onComplete` → `RestView` o `NextExercise` |
  | `RestView` | Cronómetro regresivo + botones +30s / Saltar | `onTick`, `onAdd30`, `onSkip` → `NextExercise` |
  | `NextExercise` | Transición (superserie: sin descanso) | → `ExerciseView` o `WorkoutComplete` |
  | `WorkoutComplete` | Resumen + guardar log | → `StudentDashboard` |
  | `WorkoutPaused` | App en background / llamada | → `ExerciseView` (resume) |
- [ ] **ViewModel:** `WorkoutViewModel` (Riverpod/Provider) con:
  - `currentExerciseIndex`, `currentSet`, `restTimer` (Stream/Timer)
  - Métodos: `startWorkout()`, `completeSet()`, `skipRest()`, `addRestTime(30)`, `pause()`, `resume()`, `finishWorkout()`
  - Persistencia offline: Hive guarda progreso cada set → sync al terminar
- [ ] **Pantallas:** `WorkoutScreen` (PageView/IndexedStack por estado), `ExerciseViewWidget`, `RestViewWidget`
- [ ] **Superserie logic:** Si `exercise.isSuperset && nextExercise.isSuperset` → no muestra `RestView` entre ellos
- [ ] **Tests críticos:** `testing-workflow-optimizer` → unit tests máquina estados + integration test flujo completo

---

### 3.4 Gestión de Rutinas (Trainer)
- [ ] **Acción:** CRUD rutinas + asignación a alumnos
- [ ] **Pantallas:** `RoutineListScreen`, `RoutineBuilderScreen` (drag-drop ejercicios), `AssignRoutineScreen`
- [ ] **ViewModels:** `RoutineListViewModel`, `RoutineBuilderViewModel`
- [ ] **Repositorio:** `RoutineRepository` (CRUD + `assignToStudent(studentId)`)

---

### 3.5 Evolución y Librería
- [ ] **Evolución (Student):** `EvolutionScreen` — peso, medidas, fotos (Storage), feedback semanal (formulario)
- [ ] **Librería Trainer:** `ExerciseLibraryScreen` — subir videos (Storage, reglas privacidad), CRUD ejercicios propios
- [ ] **Librería Global (Master):** `GlobalLibraryScreen` — videos accesibles por todos

---

## ✅ FASE 4: Backend Funcional, Pagos y Notificaciones (Semana 5)

### 4.1 Cloud Functions (Node.js 20 + TypeScript)
- [ ] **Acción:** Escribir y desplegar funciones
- [ ] **Agente:** `engineering-backend-architect`
- [ ] **Estructura `functions/`:**
  ```
  functions/
  ├── src/
  │   ├── index.ts                 # Entry point
  │   ├── stripe/
  │   │   ├── createCheckoutSession.ts
  │   │   ├── createPortalSession.ts
  │   │   ├── handleWebhook.ts       # invoice.payment_succeeded, failed, subscription.deleted
  │   │   └── applyCoupon.ts
  │   ├── coupons/
  │   │   ├── createCoupon.ts        # Master only
  │   │   ├── validateCoupon.ts
  │   │   └── listCoupons.ts
  │   ├── notifications/
  │   │   ├── checkPaymentsDaily.ts  # Cron: daily 09:00
  │   │   └── sendPaymentReminder.ts
  │   └── utils/
  │       ├── firebase-admin.ts
  │       └── stripe-client.ts
  ├── package.json
  └── tsconfig.json
  ```
- [ ] **Funciones clave:**
  1. **`createCheckoutSession`** (callable) — crea sesión Stripe para plan Trainer (monthly/yearly/lifetime)
  2. **`handleWebhook`** (HTTP) — escucha eventos Stripe, actualiza `trainer.subscriptionStatus` en Firestore
  3. **`checkPaymentsDaily`** (scheduled: `0 9 * * *`) — query `students where isPaidExternally == false && paymentDueDate <= today` → `sendPaymentReminder`
  4. **`sendPaymentReminder`** — FCM a `student.fcmToken` + crea doc en `notifications` subcolección
  4. **`createCoupon` / `validateCoupon`** — Master genera, Trainer/Student valida en Paywall
- [ ] **Secrets:** `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET` en `firebase functions:secrets:set`
- [ ] **Despliegue:** `firebase deploy --only functions`

---

### 4.2 Integración Stripe en Flutter
- [ ] **Acción:** Paywall + flujo suscripción
- [ ] **Agente:** `engineering-mobile-app-builder` (con soporte `backend-architect`)
- [ ] **Pantallas:** `PaywallScreen` (planes, input cupón, botón "Suscribirse"), `PaymentSuccessScreen`
- [ ] **ViewModel:** `SubscriptionViewModel` — `loadPlans()`, `purchase(planId, coupon?)`, `restorePurchases()`
- [ ] **Lógica trial:** En `TrainerDashboardViewModel` → `isInTrial = DateTime.now().difference(createdAt).inDays < 7`
- [ ] **Webhook handling:** Cloud Function actualiza `trainer.subscriptionStatus` → app escucha stream y quita paywall

---

### 4.3 Notificaciones Push (FCM)
- [ ] **Acción:** Setup FCM + permisos + handling foreground/background
- [ ] **Agente:** `engineering-mobile-app-builder`
- [ ] **Implementación:**
  - `FirebaseMessaging.instance.requestPermission()`
  - `onTokenRefresh` → guarda `fcmToken` en `users/{uid}.fcmToken`
  - `FirebaseMessaging.onMessage` (foreground) → local notification + update badge
  - `FirebaseMessaging.onMessageOpenedApp` → navega a `NotificationCenterScreen`
  - `NotificationCenterScreen` + `NotificationViewModel` — lista notificaciones (subcolección `notifications`), marca leídas

---

## ✅ FASE 5: Seguridad y QA (Semana 6) — CRÍTICA

### 5.1 Reglas de Seguridad Firestore / Storage
- [ ] **Acción:** Escribir, testear y desplegar `firestore.rules` + `storage.rules`
- [ ] **Agente:** `engineering-security-engineer`
- [ ] **Reglas Firestore (esquema):**
  ```javascript
  rules_version = '2';
  service cloud.firestore {
    match /databases/{database}/documents {
      // Users
      match /users/{userId} {
        allow read: if request.auth != null && (request.auth.uid == userId || get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Master');
        allow write: if request.auth != null && request.auth.uid == userId; // solo propio perfil
        // Trainer puede leer sus estudiantes
        allow read: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Trainer' && resource.data.trainerId == request.auth.uid;
      }
      // Routines
      match /routines/{routineId} {
        allow read: if request.auth != null && (resource.data.trainerId == request.auth.uid || resource.data.studentId == request.auth.uid);
        allow write: if request.auth != null && request.auth.uid == resource.data.trainerId;
      }
      // Exercises (librería trainer)
      match /trainers/{trainerId}/exercises/{exerciseId} {
        allow read: if request.auth != null && (request.auth.uid == trainerId || get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Master');
        allow write: if request.auth != null && request.auth.uid == trainerId;
      }
      // Global exercises (Master)
      match /globalExercises/{exerciseId} {
        allow read: if request.auth != null;
        allow write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Master';
      }
      // Notifications
      match /users/{userId}/notifications/{notificationId} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
      // Logs de entrenamiento
      match /workoutLogs/{logId} {
        allow read: if request.auth != null && (resource.data.studentId == request.auth.uid || resource.data.trainerId == request.auth.uid);
        allow create: if request.auth != null && request.auth.uid == request.resource.data.studentId;
      }
    }
  }
  ```
- [ ] **Reglas Storage:**
  ```javascript
  rules_version = '2';
  service firebase.storage {
    match /b/{bucket}/o {
      // Videos ejercicios trainer: trainers/{trainerId}/exercises/{exerciseId}/video.mp4
      match /trainers/{trainerId}/exercises/{exerciseId}/{allPaths=**} {
        allow read: if request.auth != null && (request.auth.uid == trainerId || get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Master');
        allow write: if request.auth != null && request.auth.uid == trainerId;
      }
      // Global exercises
      match /globalExercises/{exerciseId}/{allPaths=**} {
        allow read: if request.auth != null;
        allow write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Master';
      }
      // Fotos evolución estudiante: evolution/{studentId}/{photoId}
      match /evolution/{studentId}/{allPaths=**} {
        allow read, write: if request.auth != null && (request.auth.uid == studentId || get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Trainer' && resource.metadata.trainerId == request.auth.uid);
      }
    }
  }
  ```
- [ ] **Testing:** `firebase emulators:start` → `engineering-security-engineer` ejecuta suite de pruebas:
  - Alumno A NO puede leer perfil Alumno B
  - Trainer NO puede leer ejercicios de otro Trainer
  - Student NO puede escribir en rutinas
  - Master SÍ puede todo
  - Subida video requiere ownership

---

### 5.2 Auditoría Código y Secretos
- [ ] **Acción:** Escanear repo por secrets hardcodeados + code review seguridad
- [ ] **Agentes:** `engineering-code-reviewer` + `engineering-security-engineer`
- [ ] **Checks:**
  - `google-services.json` / `GoogleService-Info.plist` en `.gitignore`
  - `STRIPE_PUBLISHABLE_KEY` solo en `firebase_options.dart` / env vars (NO en código)
  - No API keys en `lib/`, `functions/src/`
  - `flutter analyze` + `dart analyze` sin warnings de seguridad
  - Dependencias auditadas: `flutter pub outdated` + `npm audit` en functions

---

### 5.3 Testing Funcional (QA & UAT)
- [ ] **Unit Tests (≥70% ViewModels):**
  - `AuthViewModel`, `OnboardingViewModel`
  - `WorkoutViewModel` (máquina estados, superseries, pausa/resume)
  - `SubscriptionViewModel` (trial, paywall, coupon)
  - `TrainerDashboardViewModel` (toggle pago, lista alumnos)
  - `NotificationViewModel`
- [ ] **Integration Tests (Flutter Integration Test):**
  - Flujo: Registro → Onboarding (trainer_id) → Dashboard Student
  - Flujo: Login Trainer → Crear Rutina → Asignar → Student ejecuta completa
  - Flujo: Paywall → Stripe Checkout (test mode) → Webhook → Acceso concedido
  - Flujo: Notificación pago → Tap → NotificationCenter
- [ ] **Agentes:** `testing-reality-checker` (UAT simulado) + `testing-workflow-optimizer` (generación suites)
- [ ] **Device testing:** iOS Simulator + Android Emulator (tamaños: small, medium, large, tablet)

---

## ✅ FASE 6: Despliegue y Lanzamiento (Semana 7)

### 6.1 Flavors / Environments
- [ ] **Acción:** Configurar `dev` y `prod` flavors
- [ ] **Agente:** `engineering-devops-automator`
- [ ] **Config:**
  - `flutter flavor dev` / `flutter flavor prod`
  - `firebase_options_dev.dart` / `firebase_options_prod.dart` (proyectos Firebase separados)
  - `google-services.json` dev/prod, `GoogleService-Info.plist` dev/prod
  - Bundle IDs: `com.risewavetech.wavegym.dev` / `com.risewavetech.wavegym`
  - Stripe test keys (dev) / live keys (prod) via `--dart-define`

---

### 6.2 Build Artifacts
- [ ] **Android:** `flutter build appbundle --flavor prod --release` → `build/app/outputs/bundle/prodRelease/app-prod-release.aab`
- [ ] **iOS:** `flutter build ipa --flavor prod --release --export-options-plist=ExportOptions.plist` → `.ipa`
- [ ] **Versioning:** `pubspec.yaml` version `1.0.0+1` (semver + build number)

---

### 6.3 Store Deployment
- [ ] **TestFlight (iOS):** Subir `.ipa` via Transporter / `xcrun altool` → TestFlight interno → beta testers
- [ ] **Google Play Console (Android):** Subir `.aab` → Internal Testing track → testers
- [ ] **Checklist pre-lanzamiento:**
  - [ ] Privacy Policy URL configurada en ambos stores
  - [ ] App icons / splash screens todos los tamaños
  - [ ] Permisos justificados (camera, photos, notifications)
  - [ ] Age rating completado
  - [ ] In-app purchases configuradas (Stripe no usa IAP nativo, pero declarar "No" en store)

---

## 📊 Tracking de Progreso (Actualizable por Agentes)

| Fase | Tarea | Estado | Agente Responsable | Fecha Inicio | Fecha Fin | Notas / Bloqueos |
|------|-------|--------|-------------------|--------------|-----------|------------------|
| 1.1 | Init Flutter + MVVM structure | ⬜ Pendiente | software-architect + mobile-app-builder | | | |
| 1.1 | pubspec.yaml dependencies | ⬜ Pendiente | mobile-app-builder | | | |
| 1.2 | Firebase projects + flutterfire | ⬜ Pendiente | backend-architect | | | |
| 1.3 | CI/CD GitHub Actions | ⬜ Pendiente | devops-automator | | | |
| 2.1 | Theme + Glassmorphism | ⬜ Pendiente | design-ui-designer + frontend-dev | | | |
| 2.2 | Component library (10 widgets) | ⬜ Pendiente | frontend-dev | | | |
| 2.2 | Visual validation | ⬜ Pendiente | reality-checker | | | |
| 3.1 | Auth + Onboarding screens | ⬜ Pendiente | ux-architect + mobile-app-builder | | | |
| 3.1 | Auth UseCases + Repository | ⬜ Pendiente | mobile-app-builder | | | |
| 3.1 | Unit tests Auth | ⬜ Pendiente | workflow-optimizer | | | |
| 3.2 | Trainer Dashboard | ⬜ Pendiente | ux-architect + mobile-app-builder | | | |
| 3.2 | Student Dashboard | ⬜ Pendiente | ux-architect + mobile-app-builder | | | |
| 3.3 | Workout Engine (State Machine) | ⬜ Pendiente | ux-architect + mobile-app-builder | | | **CRÍTICO** |
| 3.3 | Offline persistence (Hive) | ⬜ Pendiente | mobile-app-builder | | | |
| 3.3 | Unit/Integration tests Workout | ⬜ Pendiente | workflow-optimizer | | | **CRÍTICO** |
| 3.4 | Routine CRUD + Assign | ⬜ Pendiente | mobile-app-builder | | | |
| 3.5 | Evolution + Library screens | ⬜ Pendiente | mobile-app-builder | | | |
| 4.1 | Cloud Functions (Stripe, Coupons, Cron) | ⬜ Pendiente | backend-architect | | | |
| 4.1 | Deploy Functions + Secrets | ⬜ Pendiente | backend-architect | | | |
| 4.2 | Paywall + Stripe Flutter | ⬜ Pendiente | mobile-app-builder | | | |
| 4.3 | FCM Setup + Notification Center | ⬜ Pendiente | mobile-app-builder | | | |
| 5.1 | Firestore/Storage Rules | ⬜ Pendiente | security-engineer | | | **CRÍTICO** |
| 5.1 | Rules Testing (Emulator) | ⬜ Pendiente | security-engineer | | | **CRÍTICO** |
| 5.2 | Secrets Audit + Code Review | ⬜ Pendiente | code-reviewer + security-engineer | | | |
| 5.3 | Unit Tests (≥70%) | ⬜ Pendiente | workflow-optimizer | | | |
| 5.3 | Integration Tests (Flujos core) | ⬜ Pendiente | reality-checker + workflow-optimizer | | | **CRÍTICO** |
| 5.3 | Device Testing (iOS/Android) | ⬜ Pendiente | reality-checker | | | |
| 6.1 | Flavors Dev/Prod | ⬜ Pendiente | devops-automator | | | |
| 6.2 | Build App Bundle + IPA | ⬜ Pendiente | devops-automator | | | |
| 6.3 | TestFlight + Play Console Deploy | ⬜ Pendiente | devops-automator + project-manager | | | |

**Estados:** ⬜ Pendiente | 🟡 En Progreso | ✅ Completado | 🔴 Bloqueado | ⏭️ Pospuesto

---

## 🔑 Secrets & Configuración (Referencia Rápida)

| Secret / Config | Dónde se define | Flavors |
|-----------------|-----------------|---------|
| `STRIPE_SECRET_KEY` | `firebase functions:secrets:set` | dev / prod (distintas) |
| `STRIPE_WEBHOOK_SECRET` | `firebase functions:secrets:set` | dev / prod |
| `STRIPE_PUBLISHABLE_KEY` | `--dart-define=STRIPE_PUBLISHABLE_KEY=pk_...` | dev / prod |
| `FIREBASE_PROJECT_ID` | `flutterfire configure` (auto) | dev / prod |
| `GOOGLE_SERVICES_JSON` | `android/app/` (gitignored) | dev / prod |
| `GOOGLE_SERVICE_INFO_PLIST` | `ios/Runner/` (gitignored) | dev / prod |
| `APP_STORE_CONNECT_API_KEY` | GitHub Actions Secrets | prod |
| `PLAY_CONSOLE_SERVICE_ACCOUNT` | GitHub Actions Secrets | prod |

---

## 📝 Notas para Agentes Ejecutores

1. **Leer primero:** `plan_de_implementaci_n_wave_gym.md` y `wave_gym_software_design_document.md` en este mismo directorio
2. **Actualizar este archivo:** Marcar tareas completadas, agregar notas/bloqueos en la tabla de tracking
3. **Commits:** Usar conventional commits (`feat:`, `fix:`, `chore:`, `test:`, `docs:`) + co-authored-by
4. **PRs:** Cada fase o feature grande → PR con description y checklist vinculado
5. **Testing:** No mergar a `main` sin tests pasando + code review aprobado
6. **Documentación:** Actualizar `ARCHITECTURE.md` (crear si no existe) con decisiones técnicas importantes
7. **Bloqueos:** Reportar inmediatamente en tabla → escalar a `project-manager-senior`

---

## 📎 Archivos Relacionados en Repositorio

```
wave-gym/
├── plan_de_implementaci_n_wave_gym.md      # Plan original (fuente)
├── wave_gym_software_design_document.md    # SDD (fuente)
├── TRACKING_CHECKLIST.md                   # ESTE ARCHIVO
├── lib/                                    # Código Flutter
├── functions/                              # Cloud Functions (Node.js)
├── firebase.json                           # Config Firebase
├── firestore.rules                         # Reglas seguridad
├── storage.rules                           # Reglas storage
├── .github/workflows/ci.yml                # CI/CD
├── pubspec.yaml                            # Dependencias
└── README.md                               # (crear: overview + setup)
```

---

*Generado automáticamente para orquestación de agentes IA — Wave Gym MVP*
*Última sincronización con docs fuente: 2026-11-18*

---

## 📚 Mapeo de Referencias .md por Tarea

| Fase | Tarea | Archivos .md de Referencia | Secciones Clave |
|------|-------|---------------------------|-----------------|
| **1.1** | Init Flutter + MVVM structure | `plan_de_implementaci_n_wave_gym.md` | Fase 1 → Paso 1.1 |
| | | `wave_gym_software_design_document.md` | Sección 3 (Arquitectura), 19 (Frontend) |
| **1.1** | pubspec.yaml dependencies | `plan_de_implementaci_n_wave_gym.md` | Fase 1 → Paso 1.1 (dependencias del TRD) |
| | | `wave_gym_software_design_document.md` | Sección 3 (Stack Tecnológico) |
| **1.2** | Firebase projects + flutterfire | `plan_de_implementaci_n_wave_gym.md` | Fase 1 → Paso 1.2 |
| | | `wave_gym_software_design_document.md` | Sección 3 (Backend as a Service), 21-24 |
| **1.3** | CI/CD GitHub Actions | `plan_de_implementaci_n_wave_gym.md` | Fase 1 → Paso 1.3 |
| **2.1** | Theme + Glassmorphism | `plan_de_implementaci_n_wave_gym.md` | Fase 2 → Paso 2.1 |
| | | `wave_gym_software_design_document.md` | Sección 2 (UX/UI), 9-16 (Paleta, Tipografía) |
| **2.2** | Component library (10 widgets) | `plan_de_implementaci_n_wave_gym.md` | Fase 2 → Paso 2.2 |
| | | `wave_gym_software_design_document.md` | Sección 2 (Estilo visual) |
| **2.2** | Visual validation | `plan_de_implementaci_n_wave_gym.md` | Fase 2 → Testeo |
| **3.1** | Auth + Onboarding screens | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Paso 3.1 |
| | | `wave_gym_software_design_document.md` | Sección 4.1 (Auth y Roles), 4.2 (Vinculación) |
| **3.1** | Auth UseCases + Repository | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Paso 3.1 |
| | | `wave_gym_software_design_document.md` | Sección 4.1, 4.2 |
| **3.1** | Unit tests Auth | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Testeo Continuo |
| **3.2** | Trainer Dashboard | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Paso 3.2 |
| | | `wave_gym_software_design_document.md` | Sección 4.3 (Control de Alumnos), 4.5 (Dashboard) |
| **3.2** | Student Dashboard | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Paso 3.2 |
| | | `wave_gym_software_design_document.md` | Sección 4.5 (Dashboard Alumno) |
| **3.3** | Workout Engine (State Machine) | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Paso 3.3 |
| | | `wave_gym_software_design_document.md` | **Sección 4.5 (Sistema de Entrenamiento - CORE)** |
| **3.3** | Offline persistence (Hive) | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Paso 3.3 |
| | | `wave_gym_software_design_document.md` | Sección 4.5 (Modo ejecución) |
| **3.3** | Unit/Integration tests Workout | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Testeo Continuo, Fase 5 → 3.2 |
| **3.4** | Routine CRUD + Assign | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Paso 3.2-3.3 |
| | | `wave_gym_software_design_document.md` | Sección 4.5 (Estructura datos ejercicio/rutina) |
| **3.5** | Evolution + Library screens | `plan_de_implementaci_n_wave_gym.md` | Fase 3 → Paso 3.2-3.3 |
| | | `wave_gym_software_design_document.md` | **Sección 4.6 (Seguimiento, Evolución y Librería)** |
| **4.1** | Cloud Functions (Stripe, Coupons, Cron) | `plan_de_implementaci_n_wave_gym.md` | **Fase 4 → Pasos 4.1, 4.2** |
| | | `wave_gym_software_design_document.md` | Sección 4.3 (Suscripciones), 4.4 (Notificaciones), 4.3 (Cupones) |
| **4.1** | Deploy Functions + Secrets | `plan_de_implementaci_n_wave_gym.md` | Fase 4 → Paso 4.1 |
| **4.2** | Paywall + Stripe Flutter | `plan_de_implementaci_n_wave_gym.md` | Fase 4 → Paso 4.1 |
| | | `wave_gym_software_design_document.md` | Sección 4.3 (Lógica de Acceso, Paywall, Cupones) |
| **4.3** | FCM Setup + Notification Center | `plan_de_implementaci_n_wave_gym.md` | Fase 4 → Paso 4.2 |
| | | `wave_gym_software_design_document.md` | **Sección 4.4 (Notificaciones y Recordatorios)** |
| **5.1** | Firestore/Storage Rules | `plan_de_implementaci_n_wave_gym.md` | **Fase 5 → 3.1 (Pruebas de Seguridad)** |
| | | `wave_gym_software_design_document.md` | Sección 4.2 (Vinculación), 4.6 (Librería aislada) |
| **5.1** | Rules Testing (Emulator) | `plan_de_implementaci_n_wave_gym.md` | Fase 5 → 3.1 (Firebase Emulator Suite) |
| **5.2** | Secrets Audit + Code Review | `plan_de_implementaci_n_wave_gym.md` | Fase 5 → 3.1 (Auditoría Código y Secretos) |
| **5.3** | Unit Tests (≥70%) | `plan_de_implementaci_n_wave_gym.md` | Fase 5 → 3.2 (Unit Testing) |
| **5.3** | Integration Tests (Flujos core) | `plan_de_implementaci_n_wave_gym.md` | Fase 5 → 3.2 (Integration Testing) |
| **5.3** | Device Testing (iOS/Android) | `plan_de_implementaci_n_wave_gym.md` | Fase 5 → 3.2 (emuladores iOS/Android) |
| **6.1** | Flavors Dev/Prod | `plan_de_implementaci_n_wave_gym.md` | Fase 6 → Paso 4.1 |
| **6.2** | Build App Bundle + IPA | `plan_de_implementaci_n_wave_gym.md` | Fase 6 → Pasos 4.2, 4.3 |
| **6.3** | TestFlight + Play Console Deploy | `plan_de_implementaci_n_wave_gym.md` | Fase 6 → Paso 4.3 |

---

## 🛠️ Skills Detalladas por Agente/Tarea

### Agentes de Arquitectura y Gestión
| Skill / Agente | Cuándo Usar | Qué Hace | Comando de Invocación |
|----------------|-------------|----------|----------------------|
| `project-manager-senior` | Planificación, tracking, desbloqueos, priorización | Coordina sprints, gestiona riesgos, aprueba releases | `Agent(subagent_type="project-manager-senior", ...)` |
| `engineering-software-architect` | Decisiones arquitectura global, estructura proyecto, patrones | Define MVVM, DI, boundaries, tech stack decisions | `Agent(subagent_type="engineering-software-architect", ...)` |
| `engineering-backend-architect` | Firebase, Cloud Functions, Firestore modeling, APIs | Diseña backend, data model, security rules, functions | `Agent(subagent_type="engineering-backend-architect", ...)` |

### Agentes de Diseño y Frontend
| Skill / Agente | Cuándo Usar | Qué Hace | Comando de Invocación |
|----------------|-------------|----------|----------------------|
| `design-ux-architect` | Flujos de usuario, journey maps, validación UX | Diseña flujos Auth, Onboarding, Workout engine, Dashboards | `Agent(subagent_type="design-ux-architect", ...)` |
| `design-ui-designer` | Design system, tokens, componentes visuales, glassmorphism | Crea paleta, tipografías, especs visuales, valida implementación | `Agent(subagent_type="design-ui-designer", ...)` |
| `engineering-frontend-developer` | Implementación UI Flutter, widgets, theme, state management | Codifica Theme, Widgets kit, Pantallas, ViewModels | `Agent(subagent_type="engineering-frontend-developer", ...)` |
| `engineering-mobile-app-builder` | Features completas mobile, integración nativa, performance | Builds Auth, Workout engine, Dashboards, Paywall, FCM, Offline | `Agent(subagent_type="engineering-mobile-app-builder", ...)` |

### Agentes de Seguridad y Revisión
| Skill / Agente | Cuándo Usar | Qué Hace | Comando de Invocación |
|----------------|-------------|----------|----------------------|
| `engineering-security-engineer` | **Fase 5 crítica** - Security rules, threat modeling, penetration testing | Escribe/testea Firestore/Storage rules, auditoría secrets, emulador testing | `Agent(subagent_type="engineering-security-engineer", ...)` |
| `engineering-code-reviewer` | Code review, best practices, cleanup, security scan | Revisa PRs, detecta secrets, anti-patterns, sugiere mejoras | `Agent(subagent_type="engineering-code-reviewer", ...)` |

### Agentes de Testing y QA
| Skill / Agente | Cuándo Usar | Qué Hace | Comando de Invocación |
|----------------|-------------|----------|----------------------|
| `testing-workflow-optimizer` | Generación test suites, unit tests, coverage, CI testing | Escribe unit tests ViewModels (≥70%), integration test setup | `Agent(subagent_type="testing-workflow-optimizer", ...)` |
| `testing-reality-checker` | UAT simulado, device testing, visual regression, E2E flows | Ejecuta flujos completos en emuladores, valida UX real | `Agent(subagent_type="testing-reality-checker", ...)` |
| `testing-api-tester` | Cloud Functions, Stripe webhooks, FCM, endpoints testing | Testea APIs, webhooks Stripe sandbox, cron jobs, FCM delivery | `Agent(subagent_type="testing-api-tester", ...)` |

### Agentes de Despliegue
| Skill / Agente | Cuándo Usar | Qué Hace | Comando de Invocación |
|----------------|-------------|----------|----------------------|
| `engineering-devops-automator` | CI/CD, flavors, builds, store deployment, secrets management | Configura GitHub Actions, flavors dev/prod, TestFlight, Play Console | `Agent(subagent_type="engineering-devops-automator", ...)` |

---

## 🎯 Skills Especializadas (Faltantes - Recomendadas)

| Skill | Propósito | Sustituto Temporal |
|-------|-----------|-------------------|
| `engineering-payments-specialist` | Stripe SDK avanzado, PCI DSS, webhooks complejos, subscriptions lifecycle | `engineering-backend-architect` |
| `testing-mobile-qa-automation` | Flutter Integration Test, Appium, gestos táctiles, cronómetros, visual testing | `testing-workflow-optimizer` + `testing-reality-checker` |

> **Nota:** Para invocar estos skills, usar `Skill(skill="nombre-skill")` si están disponibles, o delegar al agente sustituto.

---

## 📋 Matriz Rápida: Tarea → Skill/Agente Principal

| Tarea | Skill/Agente Principal | Skills de Apoyo |
|-------|------------------------|-----------------|
| 1.1 Init Flutter + MVVM | `engineering-software-architect` | `engineering-mobile-app-builder` |
| 1.1 pubspec.yaml | `engineering-mobile-app-builder` | - |
| 1.2 Firebase Setup | `engineering-backend-architect` | - |
| 1.3 CI/CD | `engineering-devops-automator` | - |
| 2.1 Theme | `design-ui-designer` | `engineering-frontend-developer` |
| 2.2 Components | `engineering-frontend-developer` | `design-ui-designer` (review) |
| 2.2 Visual Validation | `testing-reality-checker` | - |
| 3.1 Auth/Onboarding | `engineering-mobile-app-builder` | `design-ux-architect` (flows) |
| 3.1 Auth UseCases | `engineering-mobile-app-builder` | - |
| 3.1 Unit Tests Auth | `testing-workflow-optimizer` | - |
| 3.2 Trainer Dashboard | `engineering-mobile-app-builder` | `design-ux-architect` |
| 3.2 Student Dashboard | `engineering-mobile-app-builder` | `design-ux-architect` |
| 3.3 Workout Engine | `engineering-mobile-app-builder` | `design-ux-architect` (validar UX) |
| 3.3 Offline/Hive | `engineering-mobile-app-builder` | - |
| 3.3 Tests Workout | `testing-workflow-optimizer` | `testing-reality-checker` |
| 3.4 Routine CRUD | `engineering-mobile-app-builder` | - |
| 3.5 Evolution/Library | `engineering-mobile-app-builder` | - |
| 4.1 Cloud Functions | `engineering-backend-architect` | `testing-api-tester` (webhooks) |
| 4.1 Deploy Functions | `engineering-backend-architect` | `engineering-devops-automator` |
| 4.2 Paywall/Stripe | `engineering-mobile-app-builder` | `engineering-backend-architect` |
| 4.3 FCM/Notifications | `engineering-mobile-app-builder` | `testing-api-tester` |
| 5.1 Firestore Rules | `engineering-security-engineer` | - |
| 5.1 Rules Testing | `engineering-security-engineer` | `testing-api-tester` (emulador) |
| 5.2 Secrets Audit | `engineering-security-engineer` | `engineering-code-reviewer` |
| 5.3 Unit Tests | `testing-workflow-optimizer` | - |
| 5.3 Integration Tests | `testing-reality-checker` | `testing-workflow-optimizer` |
| 5.3 Device Testing | `testing-reality-checker` | - |
| 6.1 Flavors | `engineering-devops-automator` | - |
| 6.2 Build Artifacts | `engineering-devops-automator` | - |
| 6.3 Store Deploy | `engineering-devops-automator` | `project-manager-senior` |

---

## 🔧 Cómo Invocar un Agente (Ejemplo Práctico)

```python
# Para tarea 3.3 Workout Engine (CORE):
Agent(
    subagent_type="engineering-mobile-app-builder",
    description="Build Workout Engine State Machine",
    prompt="""
    Implementar el motor inmersivo de entrenamiento (Workout Engine) como máquina de estados.
    
    REFERENCIAS OBLIGATORIAS:
    - wave_gym_software_design_document.md → Sección 4.5 (Sistema de Entrenamiento - CORE)
    - plan_de_implementaci_n_wave_gym.md → Fase 3 → Paso 3.3
    - TRACKING_CHECKLIST.md → Fase 3.3 (esta checklist)
    
    ENTREGABLES:
    1. Entidades: Exercise, Routine (domain/entities/workout.dart con freezed)
    2. Estados: WorkoutInitial, ExerciseView, RestView, NextExercise, WorkoutComplete, WorkoutPaused
    3. WorkoutViewModel con: currentExerciseIndex, currentSet, restTimer, métodos startWorkout/completeSet/skipRest/addRestTime/pause/resume/finishWorkout
    4. Pantallas: WorkoutScreen (IndexedStack), ExerciseViewWidget, RestViewWidget (+30s, Saltar)
    5. Lógica Superseries: sin RestView entre ejercicios encadenados
    6. Persistencia offline Hive (guarda progreso cada set)
    7. Unit tests máquina de estados + Integration test flujo completo
    
    STACK: Flutter + Riverpod/Provider + freezed + hive + video_player
    """,
    effort="high"
)
```

---

## ✅ Checklist de Validación Pre-Ejecución (Para Cada Agente)

Antes de iniciar CUALQUIER tarea, el agente debe confirmar:

- [ ] **Leí** `plan_de_implementaci_n_wave_gym.md` (sección correspondiente)
- [ ] **Leí** `wave_gym_software_design_document.md` (sección correspondiente)
- [ ] **Leí** `TRACKING_CHECKLIST.md` (tarea específica + referencias)
- [ ] **Tengo** acceso al repo `C:\Users\Luis\Documents\dev\wave-gym`
- [ ] **Entiendo** la arquitectura MVVM + estructura de carpetas definida
- [ ] **Conozco** la paleta de colores y tipografías (Rise Wave Tech)
- [ ] **Sé** qué agente/habilidad soy y cuál es mi responsabilidad exacta
- [ ] **Identifiqué** dependencias/bloqueos con otras tareas
- [ ] **Actualizaré** la tabla de tracking al iniciar (🟡 En Progreso) y al terminar (✅ Completado)
- [ ] **Reportaré** bloqueos inmediatamente en la tabla → escalar a `project-manager-senior`