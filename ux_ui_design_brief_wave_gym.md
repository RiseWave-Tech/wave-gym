# UX/UI Design Brief - Wave Gym (MVP)

## 1. Filosofía de Diseño y Estilo Visual
La identidad visual de **Wave Gym** se basará en un estilo **Neumorfismo Minimalista (Soft UI)**, fusionado con la paleta de colores tecnológica de **Rise Wave Tech**. 
El objetivo del neumorfismo minimalista es crear interfaces que parezcan extruidas desde el mismo fondo, utilizando sombras suaves (claras y oscuras) para dar una sensación táctil y orgánica, sin sobrecargar la pantalla. Esto genera un entorno libre de distracciones, ideal para la concentración durante el entrenamiento.

---

## 2. Paleta de Colores
Los colores principales derivan de Rise Wave Tech. Para lograr el efecto neumórfico sobre el fondo principal, se definen valores específicos para las sombras.

*   **Fondo Principal (Base):** `#F4F1EA` (Ice White / Crema Suave)
    *   *Sombra Oscura (Neumorfismo):* `#D1CEC6` (Para dar profundidad)
    *   *Sombra Clara (Neumorfismo):* `#FFFFFF` (Para dar volumen e iluminación)
*   **Color Primario (Acción):** `#D93025` (Japan Tech Red) - *Usado para botones principales y llamadas a la acción.*
*   **Color Secundario / Hover:** `#B3241B` - *Usado para estados de botones presionados.*
*   **Texto Principal:** `#1A1A1A` (Carbon Black) - *Para títulos y lectura con alto contraste.*
*   **Texto Secundario:** `#6B6B6B` - *Para subtítulos y descripciones.*
*   **Acentos Glassmorphism (Opcional para modales):**
    *   Fondo Glass: `rgba(255, 255, 255, 0.25)` con *blur* de 10px.

---

## 3. Tipografía
Se utilizará una jerarquía de dos familias tipográficas de Google Fonts para contrastar lo tecnológico con lo altamente legible.

1.  **Orbitron:**
    *   *Uso:* Títulos principales, Cronómetros de descanso, Números grandes (peso, repeticiones), Logotipo.
    *   *Pesos:* Bold (700), Black (900).
2.  **Inter:**
    *   *Uso:* Cuerpo de texto, botones, descripciones de ejercicios, inputs de formularios.
    *   *Pesos:* Regular (400), Medium (500), SemiBold (600).

---

## 4. Sistema de Componentes (UI Kit Neumórfico)

Para lograr consistencia, los componentes seguirán estas reglas físicas:

*   **Tarjetas (Cards) - Estilo Outset (Saliente):**
    *   Fondo: `#F4F1EA`
    *   Box-Shadow: `8px 8px 16px #D1CEC6, -8px -8px 16px #FFFFFF`
    *   Bordes: Redondeados (Border-Radius: 20px)
*   **Inputs y Campos de Texto - Estilo Inset (Hundido):**
    *   Fondo: `#F4F1EA`
    *   Box-Shadow: `inset 4px 4px 8px #D1CEC6, inset -4px -4px 8px #FFFFFF`
    *   Bordes: Redondeados (Border-Radius: 12px)
    *   *Sensación:* Parecen huecos en la pantalla donde el usuario puede "escribir adentro".
*   **Botones Principales (Solid Red):**
    *   Fondo: `#D93025`
    *   Sombra: `0px 4px 10px rgba(217, 48, 37, 0.3)`
    *   Texto: Blanco (`#FFFFFF`) tipografía Inter SemiBold.
    *   *Estado Activo/Presionado:* El fondo cambia a `#B3241B` y la sombra desaparece (se hunde).

---

## 5. Descripción de Pantallas Clave (Wireframes Conceptuales)

### 5.1. Pantalla de Autenticación (Login / Onboarding)
*   **Cabecera:** Logo de Wave Gym (texto "WAVE GYM" en Orbitron, Carbon Black, con un acento rojo en la 'V' o la 'W').
*   **Cuerpo:** Dos inputs neumórficos hundidos (Email y Password).
*   **Acciones:** Botón principal rojo para "Ingresar". Debajo, un botón estilo neumórfico saliente (blanco/crema con logo de Google) para "Ingresar con Google".
*   **Bloqueo de Estudiante:** Si es nuevo, pantalla limpia con un solo input hundido grande en el centro: "Ingresa el ID de tu Entrenador".

### 5.2. Dashboard del Entrenador (Trainer Home)
*   **Header:** Saludo ("Hola, [Nombre]"), y a la derecha una "Pastilla" (Badge) hundida que muestra el ID del entrenador para copiar fácilmente.
*   **Métricas Rápidas:** Dos tarjetas neumórficas salientes en la parte superior. Una muestra "Alumnos Activos" (Número grande en Orbitron).
*   **Lista de Alumnos:** Una lista vertical. Cada fila es una tarjeta suave. Adentro: Foto circular del alumno, Nombre, y a la derecha un *Toggle Switch* neumórfico. Si está rojo, el alumno "Debe el pago". Si está hundido/gris, "Pago al día".
*   **Floating Action Button (FAB):** Un botón rojo abajo a la derecha (+) para crear rutina o agregar alumno.

### 5.3. Dashboard del Alumno (Student Home)
*   **Header:** Saludo y barra de progreso semanal (ej. "3/4 entrenamientos completados").
*   **Rutina del Día:** Una tarjeta neumórfica grande ocupando el centro. Título en Orbitron: "Día 1: Full Body". Subtítulo: "45 mins - 6 Ejercicios".
*   **Botón Principal:** Botón ancho rojo brillante "INICIAR ENTRENAMIENTO" en la parte inferior de la tarjeta.
*   **Navegación Inferior (Bottom Bar):** Barra neumórfica con íconos para: Inicio, Evolución, Ajustes.

### 5.4. Motor de Entrenamiento (Workout Player) - ¡Experiencia Inmersiva!
*   **Layout Visual:** Diseño vertical para usar con una mano en el gimnasio.
*   **Top (Multimedia):** El tercio superior de la pantalla muestra el reproductor de video del ejercicio. Si no hay video, una tarjeta oscura elegante.
*   **Centro (Datos de la Serie):** 
    *   Título del ejercicio grande.
    *   Tres "pastillas" hundidas mostrando: `Series: 3`, `Reps: 10-12`, `Peso Sugerido: 20kg`.
*   **Área de Acción (Bottom):** 
    *   Botón rojo gigante: **"MARCAR COMPLETADO"**.
*   **Estado de Descanso (Resting State):** 
    *   La pantalla hace una transición suave. El centro es dominado por un cronómetro gigante en fuente *Orbitron* (Ej: `01:30`).
    *   Debajo del reloj: Botón neumórfico saliente `+30 Seg` y un botón de texto discreto "Saltar Descanso".

### 5.5. Sección de Evolución (Progress)
*   **Gráfica:** Una gráfica de líneas minimalista (la línea es color rojo `#D93025`) incrustada dentro de una tarjeta hundida, mostrando la curva de peso.
*   **Galería de Progreso:** Una cuadrícula de fotos. Cada foto está dentro de un marco con bordes redondeados y sombra suave.
*   **Botón de Registro:** Un botón para "Añadir Métricas de Hoy" que levanta un modal con efecto *Glassmorphism* (borroso) para no perder el contexto de la pantalla de atrás.

---

## 6. Recursos y Reglas Generales

1.  **Iconografía:** Se recomienda el uso de iconos estilo **Feather Icons** o **Lucide** (trazos finos, esquinas redondeadas, limpios). El grosor del trazo debe ser de 2px para combinar con la tipografía Inter.
2.  **Sistema de Espaciado (Grid de 8px):** Para mantener la proporción, todos los márgenes y paddings deberán ser múltiplos de 8 (8, 16, 24, 32, 48).
    *   Margen lateral de la app (Safe area): `24px`.
    *   Espacio entre tarjetas: `16px`.
3.  **Elevación:** 
    *   Componentes clickeables o informativos (Cards, Botones normales) usan sombra saliente (`Outset`).
    *   Componentes donde el usuario debe introducir información o estados vacíos (Inputs, Progress bar base, Toggles apagados) usan sombra hundida (`Inset`).