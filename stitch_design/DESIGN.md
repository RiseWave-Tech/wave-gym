---
name: Wave Kinetic Neumorphic
colors:
  surface: '#fcf9f2'
  surface-dim: '#dcdad3'
  surface-bright: '#fcf9f2'
  surface-container-lowest: '#ffffff'
  surface-container-low: '#f6f3ec'
  surface-container: '#f1eee7'
  surface-container-high: '#ebe8e1'
  surface-container-highest: '#e5e2db'
  on-surface: '#1c1c18'
  on-surface-variant: '#5c403c'
  inverse-surface: '#31312c'
  inverse-on-surface: '#f3f0e9'
  outline: '#906f6b'
  outline-variant: '#e5beb8'
  surface-tint: '#bb1713'
  primary: '#b5110e'
  on-primary: '#ffffff'
  primary-container: '#d93025'
  on-primary-container: '#fff8f7'
  inverse-primary: '#ffb4a9'
  secondary: '#b5251c'
  on-secondary: '#ffffff'
  secondary-container: '#ff5b4a'
  on-secondary-container: '#610001'
  tertiary: '#006845'
  on-tertiary: '#ffffff'
  tertiary-container: '#128359'
  on-tertiary-container: '#eafff0'
  error: '#ba1a1a'
  on-error: '#ffffff'
  error-container: '#ffdad6'
  on-error-container: '#93000a'
  primary-fixed: '#ffdad5'
  primary-fixed-dim: '#ffb4a9'
  on-primary-fixed: '#410001'
  on-primary-fixed-variant: '#930004'
  secondary-fixed: '#ffdad5'
  secondary-fixed-dim: '#ffb4a9'
  on-secondary-fixed: '#410001'
  on-secondary-fixed-variant: '#920506'
  tertiary-fixed: '#94f6c4'
  tertiary-fixed-dim: '#77d9a9'
  on-tertiary-fixed: '#002113'
  on-tertiary-fixed-variant: '#005235'
  background: '#fcf9f2'
  on-background: '#1c1c18'
  surface-variant: '#e5e2db'
typography:
  display-timer:
    fontFamily: Space Grotesk
    fontSize: 56px
    fontWeight: '700'
    lineHeight: 60px
    letterSpacing: -0.04em
  display-timer-mobile:
    fontFamily: Space Grotesk
    fontSize: 44px
    fontWeight: '700'
    lineHeight: 48px
    letterSpacing: -0.03em
  headline-lg:
    fontFamily: Space Grotesk
    fontSize: 32px
    fontWeight: '700'
    lineHeight: 38px
    letterSpacing: -0.02em
  headline-lg-mobile:
    fontFamily: Space Grotesk
    fontSize: 26px
    fontWeight: '700'
    lineHeight: 32px
    letterSpacing: -0.02em
  headline-md:
    fontFamily: Space Grotesk
    fontSize: 22px
    fontWeight: '700'
    lineHeight: 28px
    letterSpacing: -0.01em
  headline-sm:
    fontFamily: Space Grotesk
    fontSize: 18px
    fontWeight: '600'
    lineHeight: 24px
    letterSpacing: 0em
  body-lg:
    fontFamily: Inter
    fontSize: 16px
    fontWeight: '500'
    lineHeight: 24px
  body-md:
    fontFamily: Inter
    fontSize: 14px
    fontWeight: '400'
    lineHeight: 20px
  body-sm:
    fontFamily: Inter
    fontSize: 12px
    fontWeight: '400'
    lineHeight: 16px
  label-lg:
    fontFamily: Inter
    fontSize: 14px
    fontWeight: '600'
    lineHeight: 18px
    letterSpacing: 0.02em
  label-md:
    fontFamily: Inter
    fontSize: 12px
    fontWeight: '600'
    lineHeight: 16px
    letterSpacing: 0.04em
  label-sm:
    fontFamily: Inter
    fontSize: 10px
    fontWeight: '600'
    lineHeight: 14px
    letterSpacing: 0.06em
rounded:
  sm: 0.25rem
  DEFAULT: 0.5rem
  md: 0.75rem
  lg: 1rem
  xl: 1.5rem
  full: 9999px
spacing:
  gutter: 1rem
  gutter-sm: 0.75rem
  margin: 1.25rem
  margin-mobile: 1rem
  space-xs: 0.25rem
  space-sm: 0.5rem
  space-md: 1rem
  space-lg: 1.5rem
  space-xl: 2rem
---

## Brand & Style

This design system blends **Minimalist Neumorphism (Soft UI)** with technical athletic precision. Tailored for forward-thinking training spaces and performance tracking, the aesthetic transforms standard flat surfaces into extruded, tactile interfaces that invite physical interaction. The interface balances warm, organic background tones with crisp digital metrics, evoking focus, mechanical discipline, and high-performance craft.

The design movement centers on dual-light extrusion—simulating physical surfaces that raise from or recess directly into the canvas. Visual noise is minimized; structural hierarchy is established through light casting, depth shifts, and selective, intense red kinetic accents rather than cluttered graphic layering. Frosted structural overlays provide depth without breaking the physical continuity of the ground plane.

## Colors

The palette operates on calibrated low-contrast neutrals paired with high-voltage tactical accents:

- **Canvas & Surface (`#F4F1EA`)**: A balanced, warm matte surface acting as the root substrate for all extrusions and insets.
- **Primary Kinetic Accent (`#D93025`)**: High-saturation performance red reserved strictly for primary execution states, active timers, completed metrics, and high-priority call-to-actions.
- **Active State (`#B3241B`)**: Compressed deep-red applied to activated press states, live recording nodes, and terminal button taps.
- **Tertiary Accent (`#027d54`)**: A precise operational green used for successful confirmations, completion states, and complementary telemetry tags.
- **Shadow Substrates**: `#D1CEC6` serves as the ambient occlusion anchor on bottom-right edges, while pure `#FFFFFF` operates as the specular highlight on top-left edges.
- **Typography & Structural Contrast**: `#1A1A1A` delivers crisp, dense contrast for numerical indicators, headers, and core readable tokens. `#6B6B6B` provides muted, secondary readability for unit labels, subheadings, and passive states.

## Typography

The typographic hierarchy bridges high-tech digital readouts with systematic legibility. 

- **Display & Headings**: Space Grotesk renders workout timers, telemetry values, and section titles. Its sharp geometric forms provide an engineered, mechanical punch suited for performance-driven interfaces.
- **Body & Controls**: Inter handles labels, body prose, navigational elements, and descriptive microcopy. Its uniform distribution ensures fast cognitive processing during high-stress workouts.
- **Numbers & Data Nodes**: Data values should always feature proportional or tabular alignment, utilizing bold Space Grotesk weights to mimic instrumentation clusters.

## Layout & Spacing

The layout model is optimized for single-thumb, mobile-first interaction anchored at a nominal `390px` width.

- **Structure**: A 4-column fluid mobile grid transitioning to an 8-column layout on medium screens. Safe boundaries enforce an outer edge margin of `1.25rem` (`20px`), reduced to `1rem` (`16px`) on ultra-compact devices.
- **Neumorphic Padding Rules**: Neumorphic shadows demand breathing room. Elements must maintain a minimum distance of `space-md` (`1rem`) to prevent overlapping dual-shadow degradation.
- **Touch Targets**: All interactive tactile nodes maintain a minimum hitbox of 48x48px, embedded within spacious vertical rhythm blocks.

## Elevation & Depth

Visual hierarchy is maintained through three primary optical tiers:

1. **Outset (Extruded Surfaces)**:
   - Primary resting state for cards, quick-action chips, and segment containers.
   - Dual-shadow formulation: `8px 8px 16px #D1CEC6, -8px -8px 16px #FFFFFF`.
   - Subtle interactive lift on hover/focus: `10px 10px 20px #D1CEC6, -10px -10px 20px #FFFFFF`.

2. **Inset (Depressed / Recessed Cavities)**:
   - Reserved for input fields, metric counters, unselected state toggles, and completed workout cells.
   - Inner-shadow formulation: `inset 4px 4px 8px #D1CEC6, inset -4px -4px 8px #FFFFFF`.

3. **Active Pressed (Mechanical Displacement)**:
   - Button press state instantly snaps from extruded to recessed: `inset 3px 3px 6px #D1CEC6, inset -3px -3px 6px #FFFFFF`.

4. **Frosted Glass Overlays (Air Layer)**:
   - Modal bottom sheets, workout completion drawers, and floating nav-bars decouple from the base surface using `background: rgba(255, 255, 255, 0.25)`, `backdrop-filter: blur(10px)`, and a boundary outline of `1px solid rgba(255, 255, 255, 0.5)`. No dark Neumorphic shadows are applied to frosted layers.

## Shapes

Neumorphic light simulation relies on continuous edge curvature to produce believable highlights and shadows.

- **Standard Containers (`rounded-lg` / 1rem)**: Used for functional metric readouts, tracking panels, and input zones.
- **Primary Cards & Canvas Blocks (`rounded-xl` / 1.5rem)**: Used for major exercise cards, workout session wrappers, and performance graphs.
- **Pill Geometry**: Toggles, status tags, and action buttons make extensive use of full radius (`9999px`) rounding to distribute top-left light smoothly across the visual surface.
- **Sharp Angles**: Strictly avoided; zero-radius geometry disrupts light blending across soft UI surfaces.

## Components

### Buttons
- **Primary CTA**: Background `#D93025`, text `#FFFFFF`, rounded to full pill shape or `rounded-xl`. Light shadow cast: `0 8px 20px rgba(217, 48, 37, 0.35)`. Active/Pressed: shifts to `#B3241B` with shadow compressed to `0 2px 6px rgba(217, 48, 37, 0.45)`.
- **Tactile Secondary Button**: Background matches surface `#F4F1EA`, text `#1A1A1A`, outset neumorphic shadow (`6px 6px 12px #D1CEC6, -6px -6px 12px #FFFFFF`). When tapped, switches to inset shadow.

### Metric Cards & Containers
- Built on `#F4F1EA` with standard outset extrusion (`8px 8px 16px #D1CEC6, -8px -8px 16px #FFFFFF`).
- Padding: `space-lg`. Internal metric displays rest inside recessed chambers for heightened tactile contrast.

### Input Fields & Search Bars
- Recessed containers using `inset 4px 4px 8px #D1CEC6, inset -4px -4px 8px #FFFFFF`.
- Background `#F4F1EA`, border: none. Text `#1A1A1A`, placeholder `#6B6B6B`.
- Focused state: add a subtle `1px` inner glow or hairline focus ring using `#D93025`.

### Chips & Segmented Controls
- Segment track is inset (`inset 3px 3px 6px #D1CEC6, inset -3px -3px 6px #FFFFFF`).
- Active segment thumb is outset (`4px 4px 8px #D1CEC6, -4px -4px 8px #FFFFFF`), rounded pill, text `#1A1A1A`.

### Checkboxes & Radio Controls
- Base resting state: `24x24px` recessed inset tray (`inset 2px 2px 4px #D1CEC6, inset -2px -2px 4px #FFFFFF`).
- Checked state: Populated with a solid `#D93025` core, projecting a soft ambient glow.

### Modals & Overlays
- Floating sheets float above the extruded base using semi-transparent frosted glass (`rgba(255, 255, 255, 0.25)`), hardware-accelerated background blur (`blur(10px)`), and a delicate perimeter stroke (`1px solid rgba(255, 255, 255, 0.5)`).