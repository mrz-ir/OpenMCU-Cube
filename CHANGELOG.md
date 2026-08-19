# Changelog

All notable changes to OpenMCU Cube are documented in this file.

The format loosely follows [Keep a Changelog](https://keepachangelog.com/) and this
project uses [Semantic Versioning](https://semver.org/).

---

## 📜 A note on the early history

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
1.5.7.** The “Earlier versions” section below is a **best-effort reconstruction**
made from those inline code comments; it may be missing details and is approximate
in places.

**From v1.5.7 onward** the project is published on GitHub, and this changelog is
maintained properly with every release.

---

## [1.5.8] — 2026-08-19

### Added
- **Timer Waveform Designer** — a graphical, time-based view inside every enabled
  timer card:
  - **Signal preview:** shows the actual output waveform of every assigned channel
    pin (CHx) and, for advanced timers, the complementary output (CHxN) — the X
    axis is real time (µs/ms) and several PWM periods are drawn so the signal
    shape is clear.
  - **Mode-aware diagrams:** PWM Generation (multi-cycle square waves with pulse
    markers), Output Compare (toggle at match), Input Capture (input + capture
    markers), and Encoder (quadrature, 90° phase).
  - **Graphic numeric control:** PSC and ARR sliders + editable boxes, and a
    **per-channel Pulse** slider + number + High/Low polarity button. The existing
    numeric fields in the card stay fully in sync (two-way).
  - **Target frequency / period:** the frequency and period boxes in the card are
    both display and input — type `1kHz`, `20k`, `0.5M`, `100Hz`, or `1ms`,
    `100us`, `20ns` and press Enter; PSC/ARR are computed automatically for the
    closest achievable signal.
  - **Dead-time & complementary outputs (advanced timers only):** enable CHxN,
    set dead-time with one slider synced to two editable boxes (time in ns ⇄ DTG
    register 0–255), a live zoomed edge view shows the both-low dead-time gap, and
    the DTG max range scales with the timer clock (tDTS).
  - **Capability-aware:** only channels with an assigned pin are shown; advanced
    features appear only for timers that actually have complementary channels
    (e.g. TIM1, TIM16, TIM17).
  - **Always visible once a timer is enabled:** the waveform section (with PSC/ARR
    sliders, frequency/period boxes and, for advanced timers, dead-time) appears as
    soon as a timer is enabled — even before pins are assigned, a guided placeholder
    explains how to assign channel pins to see the live signal preview.
  - **Help buttons (`?`)** on the waveform section and on the Complementary &
    Dead-time section, with English tooltips explaining what each control does.
- **Code generation:** per-channel Pulse values are written individually
  (`Pulse = <pulseN>` per channel), channel + complementary output are merged into
  a single `TIM_OCxInit`/`HAL_TIM_PWM_ConfigChannel` call (no more last-call-wins
  overwrites), and dead-time is emitted for advanced timers:
  - PY32 HAL: `TIM_BreakDeadTimeConfigTypeDef` + `HAL_TIMEx_ConfigBreakDeadTime` +
    `HAL_TIMEx_PWMN_Start`.
  - HK StdPeriph: `TIM_BDTRInitTypeDef` + `TIM_BDTRConfig` + `TIM_OutputNState`
    + `TIM_CtrlPWMOutputs`.
- **Theme-consistent numeric inputs:** all waveform number boxes use the app's
  theme variables (dark/light).

### Fixed
- The generated timer code no longer emits two conflicting `TIM_OCxInit` calls
  when both CHx and CHxN are assigned.

---

## [1.5.7] — 2026-08-19 — first public release

### Fixed
- **HK32 GPIO output mode (build fix):** configuring a pin as GPIO Output on the
  non-F1 HK families (**HK32F030M, HK32F0301M, HK32C030**) generated
  `GPIO_Mode_Out_PP` / `GPIO_Mode_Out_OD`, macros that **do not exist** in those
  StdPeriph drivers (they only define `GPIO_Mode_OUT` + a separate
  `GPIO_OType_PP/OD` field) — the build failed with "undeclared identifier".
  The generator now emits the correct `GPIO_Mode_OUT` + `GPIO_OType_PP/OD` pair
  for those families, while **HK32F103** keeps its valid `GPIO_Mode_Out_PP/OD`.
- Verified against the actual embedded driver headers that **every** `GPIO_*` /
  `GPIOMUX_*` token emitted for HK32F030M, HK32F0301M, HK32C030 and HK32F103
  (Input with all pull options, Output PP/OD, Alternate Function, Analog) exists
  in the shipped SDK — no more undefined-macro build errors for GPIO config.
- **Clock tree diagram — layout:** reworked the bottom (peripheral-bus) area so
  labels and lines no longer overlap: larger peripheral boxes with correctly spaced
  title/value text, bus names no longer collide with the first box, and multi-bus
  devices (e.g. HK32F103 APB1/APB2) are stacked with dynamic spacing.
- **Clock tree diagram — draw order:** all lines/paths are now rendered *under* the
  boxes (in both themes) instead of on top of them. Routing was cleaned up so no
  wire crosses a box: LSE→RTC joins via the main trunk, the second APB bus is fed
  along a channel below the low-speed trunk and down the far left edge, and
  peripheral connectors stop at the box top edge.
- **Help tooltips** now open **below** the `?` button instead of above, so they are
  no longer clipped when the button sits near the top of the page.
- **Light theme — pin labels:** pins without a special role (and the project-name
  input box) had text in the same color as their background and were unreadable.
  Pin-name labels now use dark text on white boxes (reserved clock pins keep amber),
  and the selected-pin outline is dark too.
- **Light theme — active clock path:** the highlighted route is now clearly drawn in
  green (the previous gray overlay made it invisible), with a thicker stroke and a
  subtle glow.
- **Mobile / tablet layout:** the header no longer wraps the logo below the device
  dropdown and tabs on narrow windows — the logo stays on top down to phone sizes.
- Darker background for better readability in the default (dark) theme.

### Added
- **Light / Dark theme toggle** in the top bar (choice is saved in `localStorage`).
- **Inline help buttons (`?`)** with English hover tooltips for every section:
  Device selector, Pin Configuration, Package view, Peripherals, Clock sources,
  Clock tree, Code generation, and the Upload zone. Each tooltip explains what its
  section does.
- The code-generation panel's help text now summarizes how to continue editing a
  previous project (upload `main.c` + `project_settings.json`), the hint under the
  generated code explains the per-series code style (HAL vs StdPeriph) and the ZIP
  contents, and the footer carries the full official disclaimer.

### Changed
- Version bumped to **1.5.7** everywhere (badge, generated-code headers, document
  title, ZIP/uvprojx metadata).
- This is the first version prepared for **public release on GitHub** (README,
  LICENSE, .gitignore and this changelog added).

---

## Earlier versions (reconstructed — approximate)

> ⚠️ These entries are reconstructed from inline code comments and may be
> incomplete. See “A note on the early history” above.

### [1.5.6]
- **Full SDK payload embedded** — the single-file app now carries driver *sources*,
  headers *and* startup files; the generated ZIP ships a complete project instead
  of headers only.
- **Keil (.uvprojx) build fix** — repaired malformed XML that made uVision report
  the project as corrupt, and added the missing `.c` sources / startup `.s`.
  Verified by actually compiling and linking a generated project with an ARM
  toolchain.
- **Verified HK32 Keil DFP backend** — the generator uses exact Device / IROM /
  IRAM, SVD, startup and Flash-Algorithm data taken from the official HKMicroChip
  `.PACK` files.
- **Pin marking for SYSTEM peripherals** — restored the “Mark Pins” action for
  MCO, EVENTOUT, IR_OUT and SWD (regression from 1.5.5).
- **Byte-aware ZIP writer** — ZIP entries can now carry raw bytes, not just strings.

### [1.5.5]
- Internal UI release.
- Introduced a regression: the peripheral card only rendered action buttons for
  non-`auto` peripherals, so System-group peripherals (MCO, EVENTOUT, IR_OUT, SWD)
  could not be pin-marked (fixed in 1.5.6).

### [1.5.4]
- First full-ZIP code generation (drivers + headers).
- Known limitations at this version: the ZIP writer handled strings only, and the
  HK32 `.uvprojx` generator emitted malformed XML (both fixed in 1.5.6).

### v1.4 – v1.5.2 (hardening)
- Device-specific clock limits and the **HK32F0301M** database.
- Real IOMUX handling for HK32F030M / HK32F0301M.
- Real TIM mode generation (PWM / OC / IC / Encoder).
- Real ADC channel configuration.
- I2C timing fields for STM32F0-style StdPeriph.
- CAN bitrate calculation for HK32F103.
- APB1 / APB2 limit validation with compile-time-visible warnings instead of fake
  values.

### Before v1.4
- Not documented, even in code comments — nothing reliable is known about these
  versions.
