# Plan de Implementación y Orquestación de IA - Wave Gym (MVP)

Este documento detalla la hoja de ruta paso a paso para la construcción de Wave Gym, incluyendo la estrategia de pruebas, seguridad y la asignación óptima de los agentes de Inteligencia Artificial disponibles en el entorno de desarrollo.

## 1. Mapeo de Agentes IA (Skills Disponibles)

Basado en el directorio de habilidades del sistema, los agentes se orquestarán de la siguiente manera:

* **Gestión y Arquitectura:** `project-manager-senior`, `engineering-software-architect`, `engineering-backend-architect`.
* **Diseño y UX:** `design-ux-architect`, `design-ui-designer`. *(Se prescinde de `design-whimsy-injector` y `design-visual-storyteller` para el MVP, manteniendo el foco técnico).*
* **Desarrollo:** `engineering-mobile-app-builder`, `engineering-frontend-developer`.
* **Seguridad y Revisión:** `engineering-security-engineer`, `engineering-code-reviewer`.
* **Pruebas y QA:** `testing-api-tester`, `testing-reality-checker`, `testing-workflow-optimizer`.
* **Despliegue:** `engineering-devops-automator`.

### 1.1. Skills Faltantes (Recomendadas para añadir al framework)
* **`engineering-payments-specialist`:** Experto específico en SDK de Stripe y normativas PCI DSS (puede ser suplido temporalmente por el `backend-architect`).
* **`testing-mobile-qa-automation`:** Un agente dedicado exclusivamente a escribir pruebas de integración visuales (Appium / Flutter Integration Test) para gestos táctiles complejos, diferente a un API tester.

---

## 2. Fases de Construcción (Sprints de Implementación)

### Fase 1: Setup Core y Arquitectura Base (Semanas 1)
**Objetivo:** Inicializar el entorno, repositorios y conexiones a Firebase.
* **Paso 1.1:** Inicialización del proyecto Flutter, configuración de `pubspec.yaml` (dependencias del TRD) y estructura MVVM.
  * *Agente a cargo:* `engineering-software-architect` + `engineering-mobile-app-builder`.
* **Paso 1.2:** Creación de proyectos en Firebase (Auth, Firestore, Storage) y vinculación con Flutter (Firebase CLI).
  * *Agente a cargo:* `engineering-backend-architect`.
* **Paso 1.3:** Setup de CI/CD básico (GitHub Actions o Codemagic) para compilar la app automáticamente.
  * *Agente a cargo:* `engineering-devops-automator`.

### Fase 2: Diseño UI y Componentes Base (Semana 2)
**Objetivo:** Traducir el UX/UI Brief en código utilizable.
* **Paso 2.1:** Configuración de `core/theme` (Colores Rise Wave, tipografías, neumorfismo).
  * *Agente a cargo:* `design-ui-designer` (para validar tokens) + `engineering-frontend-developer` (para codificar el tema).
* **Paso 2.2:** Creación de Widgets reutilizables (Botones Neumórficos, Inputs, Tarjetas, reproductores de video).
  * *Agente a cargo:* `engineering-frontend-developer`.
* **Testeo:** Revisión visual de componentes contra Figma/Wireframes.
  * *Agente a cargo:* `testing-reality-checker`.

### Fase 3: Desarrollo de Features - Lógica de Cliente (Semanas 3 y 4)
**Objetivo:** Flujos de usuario y pantallas principales.
* **Paso 3.1:** Implementar Autenticación y Onboarding (Google Sign-In, validación de ID de entrenador).
* **Paso 3.2:** Dashboard de Entrenador (Lista de alumnos, toggle de pagos) y Dashboard de Alumno.
* **Paso 3.3:** Motor Inmersivo de Entrenamiento (Máquina de estados, cronómetro, superseries).
  * *Agentes a cargo:* `design-ux-architect` (validar flujos) + `engineering-mobile-app-builder`.
* **Testeo Continuo:** Unit Tests de ViewModels.
  * *Agente a cargo:* `testing-workflow-optimizer` (para generar los test suites).

### Fase 4: Backend Funcional, Pagos y Notificaciones (Semana 5)
**Objetivo:** Cloud Functions, Stripe y trabajos programados.
* **Paso 4.1:** Escribir y desplegar Cloud Functions (Node.js) para procesar pagos de Stripe y gestionar cupones.
* **Paso 4.2:** Configurar Cron Job para chequear el `is_payment_due` y disparar notificaciones Push (FCM).
  * *Agente a cargo:* `engineering-backend-architect`.
* **Testeo:** Pruebas de endpoints y webhooks de Stripe en entorno Sandbox.
  * *Agente a cargo:* `testing-api-tester`.

---

## 3. Esquema de Testeo y Seguridad (Fase 5 - Semana 6)

Esta es la fase crítica antes de la compilación final. Los agentes de seguridad y calidad toman el control total.

### 3.1. Pruebas de Seguridad (Security Testing)
* **Validación de Reglas Firestore/Storage:** El agente probará el SDD evaluando si un "Alumno A" puede forzar la lectura del perfil del "Alumno B", o si un Entrenador puede acceder a los videos de la competencia.
  * *Herramienta:* Firebase Emulator Suite.
  * *Agente a cargo:* `engineering-security-engineer`.
* **Auditoría de Código y Secretos:** Búsqueda de claves API quemadas en el código fuente (Google Services, Stripe Publishable Key).
  * *Agente a cargo:* `engineering-code-reviewer` + `engineering-security-engineer`.

### 3.2. Pruebas de Funcionalidad (QA & UAT)
* **Unit Testing:** Cobertura mínima del 70% en los ViewModels (lógica de negocio), prestando especial atención a la lógica del cronómetro de descansos y el salto de superseries.
* **Integration Testing:** Prueba del flujo de "Registro -> Creación de Rutina -> Ejecución de Rutina" en emuladores de iOS y Android para detectar trabas en la UI.
  * *Agente a cargo:* `testing-reality-checker` (User Acceptance Testing simulado) y `testing-workflow-optimizer`.

---

## 4. Despliegue y Lanzamiento (Fase 6 - Semana 7)

* **Paso 4.1:** Configuración de flavors/entornos (Dev, Prod).
* **Paso 4.2:** Generación de App Bundles (Android) e IPA (iOS).
* **Paso 4.3:** Despliegue en TestFlight y Google Play Console (Cerrado).
  * *Agente a cargo:* `engineering-devops-automator` guiado por `project-manager-senior`.