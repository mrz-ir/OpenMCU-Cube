# Changelog

All notable changes to OpenMCU Cube are documented in this file.

The format loosely follows [Keep a Changelog](https://keepachangelog.com/) and this
project uses [Semantic Versioning](https://semver.org/).

---

## [1.6.1] — 2026-08-29

> **Release note:** v1.6.1 is the next public release after **v1.6.0**. There is
> **no public v1.5.9 release**, and the 1.6.0.x engineering sub-versions (which were
> never published as separate releases) are consolidated here under **v1.6.1**.
> The public release history is therefore: **v1.5.8 → v1.6.0 → v1.6.1**.

### Added

#### 🔀 Interrupt priority ordering
- Added an **“Interrupt Priorities”** panel on the **Code Generation** tab listing
  every currently enabled interrupt.
- Reorder with **drag-and-drop or arrow buttons**; the top of the list is the
  highest priority.
- Feeds the generated `HAL_NVIC_SetPriority` / `NVIC_InitStructure` code for both
  HAL- and StdPeriph-based chips.
- The chosen order is saved in `project_settings.json` and restored on reload.

#### 🔍 Zoomable / pannable package view
- **Scroll to zoom** — the zoom is now **anchored at the mouse cursor** (the point
  under the pointer stays fixed while scaling).
- **Click and drag to pan** around the package drawing.
- A **100%** button resets and re-centers the chip diagram; the view also resets
  automatically when the device/package changes.

#### 📦 Output Settings modal
- Choose exactly which files go into the downloaded ZIP: `main.c`, the Keil project
  (`.uvprojx` / `.uvoptx`), SDK libraries (`Drivers/`), `project_settings.json` and
  `README.txt`.
- At least one item must stay enabled.

#### ↩️ Undo / Redo
- `Ctrl+Z` / `Ctrl+Y` shortcuts plus toolbar buttons.
- One-click **Clear Pins** and **Reset Peripherals** actions from a compact history
  menu in the header.

#### 🎛 Device picker overhaul
- New device-selection modal with a **brand filter** (Puya / HK MicroChip), a
  **family filter** and **live search** by part number or package.

#### ⌕ Peripheral list search & filter
- Search box and group filter chips (Connectivity / Timers / Analog / Time /
  Display / System) above the peripheral list.

#### 🎨 New color themes
- **Midnight** — a cooler, blue-slate dark theme.
- **Paper** — a warm, parchment-toned light theme.
- The selected theme is cached in the browser (`localStorage`) and restored
  automatically the next time the app is opened.

### Changed
- Timer peripheral cards no longer show separate **Prescaler (PSC)**, **Period
  (ARR)** and **Pulse** fields in the top parameter grid — these are already
  editable via their own slider + adjacent number box in the Waveform Designer, so
  the duplicate controls were removed to reduce clutter. `Mode`, `Channel
  polarity`, `Interrupt` and (for Encoder mode) `PPR` / `Initial counter` are
  unaffected and still shown.
- Replaced the day/night toggle button with a **theme picker** (palette icon)
  offering four themes: **Dark**, **Light**, **Midnight** and **Paper**.

### Fixed
- **“Download all files (ZIP)” silently produced incomplete, non-buildable
  projects.** The function responsible for packaging driver/header files
  (`embeddedHeaderFiles`) read from an emptied legacy placeholder object, so every
  exported ZIP — and every generated Keil project’s file list — shipped with
  **zero files from `Drivers/`**. This bug predates this release. Fixed by wiring
  the ZIP / Keil-project build step to the existing, verified
  `buildProjectFiles()` pipeline. Verified: exported projects now include the full
  driver tree (~75+ files) and a `.uvprojx` that correctly references all of them.
- PSC / ARR / Pulse are still seeded with correct default values internally even
  though they are no longer shown in the generic parameter grid, so generated code
  (`htim.Init.Prescaler`, `htim.Init.Period`, etc.) is unaffected by the UI change.

### Verification
- All embedded JavaScript blocks pass syntax validation.
- Interrupt / EXTI code generation was re-verified on the real UI across all
  supported chips (PY32F030, PY32F002A, PY32F002B, HK32F030M, HK32C030, HK32F103):
  per-device IRQ vectors, `_it.c` / `_it.h` handlers and declarations,
  `HAL_*_Start_IT` / `TIM_ITConfig` / `USART_ITConfig` / `UART_ITConfig` /
  `ADC_ITConfig` output, and EXTI (grouped and per-line vectors).
- Cursor-anchored zoom was verified: the content point under the mouse cursor
  stays fixed while zooming, and the pan formulas match the viewport transform.
- ZIP export now includes the full driver tree and a `.uvprojx` referencing every
  file (verified).
- Supported MCU families are unchanged from v1.6.0 (see the table in the entry
  below).

### Documentation
- Updated `README.md` and this `CHANGELOG.md`; added `RELEASE_NOTES_1_6_1.md`.
- All screenshots in `docs/` were regenerated from the v1.6.1 application.
- The release history is presented as **v1.5.8 → v1.6.0 → v1.6.1** (no public
  v1.5.9; 1.6.0.x sub-versions consolidated under v1.6.1), with the application
  file `OpenMCU_Cube1_6_1.html` and the GitHub Pages path
  `https://mrz-ir.github.io/OpenMCU-Cube/OpenMCU_Cube1_6_1.html`.

---

## [1.6.0] — 2026-08-24

> **Release note:** v1.5.9 was **never released publicly** — there is **no intermediate
> v1.5.9 release** in the public release history. Everything that had been planned for that
> intermediate version is consolidated into this release and documented below together with
> the v1.6.0 fixes and improvements. The public release history is therefore:
> **v1.5.8 → v1.6.0**.

### Added

#### ⚡ Interrupt (IRQ) support
- Added an **Interrupt** option (`Disabled` / `Enabled`) to every supported **timer,
  UART/USART and ADC** card.
- Added automatic `MX_IRQ_Init()` generation with device-specific NVIC configuration and
  the exact IRQ vector of each supported device:
  - **PY32F030:** TIM1 → `TIM1_BRK_UP_TRG_COM` + `TIM1_CC`; TIM3/14/16/17 use their own vectors.
  - **PY32F002A:** TIM1 and TIM16.
  - **PY32F002B:** TIM1 and TIM14.
  - **HK32F103:** TIM1 update/CC, TIM2/3/4 and ADC1_2.
  - **HK32C030:** TIM1 break/update/trigger/commutation + CC, TIM2/3/14/15/16/17 and ADC.
  - **HK32F030M / HK32F0301M:** TIM1 break + update/trigger/commutation + CC, TIM2 and ADC1.
- HAL targets use the appropriate interrupt-start APIs: `HAL_TIM_PWM_Start_IT`,
  `HAL_TIM_OC_Start_IT`, `HAL_TIM_IC_Start_IT`, `HAL_ADC_Start_IT`.
- StdPeriph targets use the corresponding `TIM_ITConfig`, `USART_ITConfig`,
  `UART_ITConfig` (HK32C030) and `ADC_ITConfig`.
- Generated `_it.c` / `_it.h` files contain the required IRQ handlers with `USER CODE
  BEGIN/END` blocks; Cortex-M3 targets (HK32F103) receive the NVIC priority-group
  configuration once.

#### 🔔 External interrupts (EXTI)
- Added an **External interrupt (EXTI)** selector to GPIO pin configuration: None /
  Rising / Falling / Rising + Falling.
- Added generated `MX_EXTI_Init()` with GPIO interrupt mode and NVIC configuration
  (HAL), or `GPIO_EXTILineConfig` + `EXTI_Init` (StdPeriph).
- Added generated `EXTIx_IRQHandler` handlers with `USER CODE` blocks, using the correct
  grouped vectors (PY32 / HK32C030) or per-line vectors (HK32F030M/0301M, HK32F103).

#### 🕐 Timer Waveform Designer
- Added a graphical, **time-based** waveform preview to every enabled timer card,
  covering PWM Generation, Output Compare, Input Capture and Encoder.
- Displays assigned **CHx** outputs and, where supported, complementary **CHxN** outputs.
- Added graphical **PSC / ARR** sliders plus editable numeric fields, all synchronized
  with the existing timer configuration.
- Added **per-channel Pulse** controls and polarity selection; fixed the case where one
  channel's configuration could overwrite another's.
- **Duty-cycle** control is shown **only in PWM mode**; in Output Compare mode the value
  is treated as a compare **match** (no misleading duty label).
- Frequency / period can be entered directly (`1kHz`, `20k`, `0.5M`, `100Hz`, `1ms`,
  `100us`, `20ns`) and the closest achievable PSC/ARR combination is computed
  automatically.
- Advanced timers expose **complementary-output and dead-time** controls (ns ⇄ DTG
  register, timer-clock-dependent range, live zoomed edge visualization).
- The waveform section is visible as soon as a timer is enabled, even before pins are
  assigned, with guidance on assigning channel pins.
- Added contextual `?` help and theme-consistent numeric inputs.

#### 🔄 Encoder configuration
- Dedicated encoder controls: **pulses per revolution (PPR)**, **initial counter value**,
  steps per revolution (PPR × 4), counter range, and **maximum RPM before overflow**.
- Added a quadrature waveform diagram showing the 90° phase relationship.
- Generated code initializes the timer counter to the configured initial value using the
  appropriate HAL or StdPeriph API.

#### 🟢 PY32 device-specific support
- PY32 devices are no longer treated as one generic STM32-compatible family; each uses
  its own SDK behavior.
- **PY32F002B:** correct HSE-bypass handling, `HSIDiv` configuration, PY32-specific HSI
  calibration constants, and clock initialization per the actual PY32F002B SDK API.
- **PY32F030 / PY32F002A:** corrected clock-source and HSI handling to match the bundled
  vendor SDK APIs.

#### 🧭 HK32F030M / HK32F0301M clock & IOMUX
- Fixed `RCC_EXTCmd()` generation by passing the required external-clock selector
  argument, with external-clock input selection and the corresponding GPIO/IOMUX setup.
- Corrected special HK32F030M alternate-function cases that need vendor-specific IOMUX
  configuration beyond a generic AF call.
- Added BEEPER support where exposed by the device database/SDK.
- Corrected HK32F0301M HSI/clock configuration and memory metadata (48 MHz capability
  reflected instead of generic HK32F030M assumptions).

#### 🚀 HK32F103 clock, memory & remap
- Corrected RAM metadata to the device-supported **20 KB** value where applicable.
- Corrected HSI-based PLL path, device-specific HSI divider and HSI28/HSI56 handling,
  high-frequency Flash-latency, `SystemCoreClock` and SysTick initialization.
- Timer-clock calculation now accounts for the APB-prescaler timer-clock multiplier.
- Added device-specific F1-style GPIO remap handling for supported TIM / SPI / I2C /
  SWJ-JTAG mappings.
- Improved parsing of composite timer channel names such as `TIM2_CH1_ETR`.

#### 🧩 HK32C030 improvements
- Added **TIM6** to the peripheral model and generation (enable without a GPIO channel).
- Corrected HK32C030 **UART** API usage (not STM32-style USART) across initialization,
  clock-enable, interrupt configuration and IRQ handling.
- Improved I2C timing generation to use the device-specific API/field model.
- Corrected `TIM2_CH1_ETR` handling.

#### 🛡 Validation & export safety
- Added a centralized **validation gate** before source/project export; configurations
  that fail mandatory hardware or generator validation are rejected.
- Added stricter validation for:
  - **Clock:** SYSCLK, HCLK, APB1, APB2.
  - **Timer:** prescaler, ARR, PWM pulse width, encoder channel requirements,
    complementary `CHxN` constraints.
  - **Peripheral:** unsupported or incomplete implementations (no misleading init code).
  - **UART:** invalid / non-positive baud rates.
- Added stricter exact-device validation for supported HK32 configurations.
- Added the machine-readable validation API: `window.OpenMCUValidation()`.

#### 🔌 Offline / standalone operation
- Removed the embedded Cloudflare challenge script — the standalone HTML application no
  longer depends on that hosting-specific script.

### Changed

#### 🛠 GPIO generation
- HK32 **non-F1** StdPeriph devices now generate `GPIO_Mode_OUT` + `GPIO_OType_PP` /
  `GPIO_OType_OD`; HK32F103 retains the valid F1-style `GPIO_Mode_Out_PP` / `Out_OD`.
- GPIO AF and vendor-specific IOMUX configuration are separated where required.

#### ⏱ Clock generation
- Clock code is less dependent on generic STM32 assumptions and follows the selected
  vendor SDK (device-specific Flash latency, PLL, HSI/HSE, APB and timer-clock rules).
- External-clock configurations retain the selected source instead of treating HSE as a
  generic single input.

#### 💾 Keil / ZIP generation
- Keil projects use device-specific memory and startup metadata; HK32 memory definitions
  were corrected where generic family values did not match the selected device.
- ZIP generation remains **byte-aware**, preserving binary startup/driver payloads; the
  ZIP continues to ship the full embedded driver sources, headers and startup files.

#### 🔄 Project restore
- `main.c` + `project_settings.json` restore MCU, package, pins, peripherals, clock and
  project name; `USER CODE BEGIN/END` blocks are preserved.

#### 🎨 UI
- **Clock tree:** reworked peripheral-bus layout (spacing, bus labels, multi-bus
  rendering, APB1/APB2), paths drawn beneath boxes, no wires crossing boxes, improved
  LSE→RTC and APB routing.
- **Light theme:** readable pin labels, project-name input contrast, selected-pin
  outline, clock-pin visibility and clearly visible active clock paths.
- **Dark theme:** improved default background contrast and overall readability.
- **Responsive:** mobile/tablet header fixed (logo no longer wraps beneath controls);
  waveform/configuration panels usable on small screens.
- **Inline help:** `?` buttons for device selector, pin config, package view,
  peripherals, clock sources, clock tree, code generation, upload zone, waveform and
  dead-time — tooltips open **below** the button to avoid clipping.

### Fixed
- **HK32 build — `serKey is not defined`:** fixed a JavaScript scope issue affecting HK32
  project generation; added a global `serKey()` helper so earlier HK32/Keil generator
  modules resolve the function correctly, while preserving the internal implementation
  used by later modules.
- **HK32 CAN bus mapping:** corrected the canonical HK32F103 **CAN1 → APB1** mapping;
  removed the dependency on a later runtime patch that relocated CAN1.
- **HK32F030M:** `RCC_EXTCmd()` missing selector argument; external-clock/IOMUX
  configurations; special AF mappings needing vendor IOMUX.
- **HK32F103:** RAM metadata, high-frequency Flash latency, HSI28/HSI56 paths,
  `SystemCoreClock`/SysTick assumptions, missing F1 remap generation, `TIM2_CH1_ETR`
  parsing.
- **HK32C030:** TIM6 visibility/generation, UART/USART API mismatches, I2C timing
  assumptions.
- **PY32:** clock-generation assumptions that treated distinct PY32 SDKs as one
  STM32-compatible implementation.
- **Timer:** conflicting duplicate `TIM_OCxInit` when CHx and CHxN are both configured;
  per-channel Pulse overwrites; dead-time range now signal-limited.
- **GPIO:** output-mode macro mismatch on HK32 non-F1 families.
- **IRQ/EXTI:** wrong vectors and device-specific interrupt APIs; grouped vs per-line
  EXTI.
- **UI:** light-theme pin labels / selected-pin contrast / active clock-path visibility;
  clock-tree wire/box overlap; help-tooltip clipping; mobile header.

### Internal
- Added/retained `window.OpenMCUValidation()` and the centralized export gate.
- Version metadata updated to **1.6.0** (application badge, document title, generated
  project metadata); the 1.6.0.x engineering sub-versions are consolidated under 1.6.0.
- Preserved the existing SDK and Keil project-generation infrastructure.

### Verification
- All embedded JavaScript blocks pass syntax validation.
- HK32 `serKey()` resolution was specifically verified after the fix.
- HK32 CAN1 → APB1 mapping was verified.
- Export validation hooks were verified.

### Supported MCU families (v1.6.0)

| Family | Vendor | Max core clock |
|--------|--------|---------------:|
| PY32F030 | Puya | 48 MHz |
| PY32F002A | Puya | 24 MHz |
| PY32F002B | Puya | 24 MHz |
| HK32F030M | Hangshun | 32 MHz |
| HK32F0301M | Hangshun | 48 MHz |
| HK32C030 | Hangshun | 64 MHz |
| HK32F103 | Hangshun | 120 MHz |

### Validation philosophy
- This release does **not** introduce a compiler-based self-validation system; previous
  attempts were considered unreliable.
- Device-specific correctness is based on the **bundled vendor SDK headers, source
  files, startup files and device metadata**, plus explicit family-specific generation
  paths.
- Generated projects should still be compiled and checked in the target toolchain before
  hardware deployment.

### Documentation
- Updated `README.md`, this `CHANGELOG.md` and the new `RELEASE_NOTES_1_6_0.md` release notes.
- All documentation presents the release history as **v1.5.8 → v1.6.0** (no public
  v1.5.9), with the application file `OpenMCU_Cube1_6_0.html` and the GitHub Pages path
  `https://mrz-ir.github.io/OpenMCU-Cube/OpenMCU_Cube1_6_0.html`.

---

## [1.5.8] — 2026-08-19

### Added
- Timer Waveform Designer with time-based signal previews (PWM / Output Compare /
  Input Capture / Encoder).
- PSC/ARR graphical controls and synchronized numeric inputs; per-channel Pulse and
  polarity; frequency/period input with automatic PSC/ARR calculation.
- Complementary-output and dead-time controls for supported advanced timers, with live
  dead-time visualization and DTG representation.
- Encoder-specific controls and quadrature visualization.
- Contextual waveform help buttons.

### Fixed
- Generated timer code no longer emits conflicting duplicate `TIM_OCxInit` calls when
  both CHx and CHxN are assigned.
- Waveform numeric inputs use the application's theme variables.

---

## Earlier versions (reconstructed — approximate)

> ⚠️ These entries are reconstructed from inline code comments and may be incomplete.
> See the original project history for details.

### [1.5.7] — first public release
- Fixed HK32 GPIO output-mode generation for non-F1 HK32 families.
- Verified GPIO/GPIOMUX tokens against the embedded HK32 SDK headers.
- Reworked clock-tree layout and draw order; fixed help-tooltip clipping.
- Improved light-theme pin labels and active clock-path visibility; improved
  mobile/tablet header layout.
- Added light/dark theme support and inline help buttons.
- Added README, LICENSE, `.gitignore` and public-release documentation.

### [1.5.6]
- Embedded the complete SDK payload (driver sources, headers and startup files).
- Fixed malformed Keil `.uvprojx` generation; added missing `.c` sources and startup `.s`.
- Added HK32 Keil DFP metadata (Device/IROM/IRAM/SVD/startup/Flash Algorithm).
- Restored pin marking for system peripherals (MCO, EVENTOUT, IR_OUT, SWD).
- Added byte-aware ZIP writing.

### [1.5.5]
- Internal UI release; introduced a temporary regression in system-peripheral action
  buttons (fixed in 1.5.6).

### [1.5.4]
- Introduced full-ZIP code generation with driver sources and headers.
- Known limitations: string-only ZIP writer and malformed HK32 `.uvprojx` (both fixed in
  1.5.6).

### v1.4 – v1.5.2
- Added device-specific clock limits and the HK32F0301M database.
- Added real IOMUX handling for HK32F030M / HK32F0301M.
- Added real timer-mode generation (PWM, OC, IC, Encoder) and ADC channel configuration.
- Added I2C timing fields for STM32F0-style StdPeriph and CAN bitrate calculation for
  HK32F103.
- Added APB1/APB2 limit validation with compile-time-visible warnings.

### Before v1.4
- No reliable historical changelog is available.


### 📜 A note on the early history

OpenMCU Cube was developed and ran for **several versions before 1.5.7**, but no
proper changelog was ever kept. Two reasons:

1. **The project was still incomplete / work-in-progress.** Features, driver
   payloads and generated code changed rapidly and were not stable enough to be
   documented as releases. Some modules even shipped known-broken pieces that were
   only repaired in later versions (see the notes under 1.5.4 / 1.5.5 below).
2. **It was never intended to be published.** There was no GitHub repository and no
   release discipline — changes were tracked only as short comments scattered inside
   the single HTML file, and the version number was simply overwritten in place.

Because of this, **there is no complete, authoritative log for versions before
1.5.7.** The “Earlier versions” section above is a **best-effort reconstruction**
made from those inline code comments; it may be missing details and is approximate
in places.

**From v1.5.7 onward** the project is published on GitHub, and this changelog is
maintained properly with every release.
