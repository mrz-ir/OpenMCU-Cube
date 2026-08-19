<div align="center">

# 🧊 OpenMCU Cube

### An STM32CubeMX-style configurator for **HK32** & **PY32** microcontrollers

<p align="center">
  <a href="https://mrz-ir.github.io/OpenMCU-Cube/OpenMCU_Cube1_5_7.html" target="_blank">
    <img src="https://img.shields.io/badge/⚡_Try_Now-OpenMCU_Cube_v1.5.7-4F46E5?style=for-the-badge" alt="Try Now" height="80">
  </a>
</p>

**Pinout · Clock tree · Code generation — in a single offline HTML file.**

[![Version](https://img.shields.io/badge/version-1.5.7-33d6c0?style=flat-square)](OpenMCU_Cube1_5_7.html)
[![License: MIT](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)
[![No dependencies](https://img.shields.io/badge/dependencies-none-5b8def?style=flat-square)](#-getting-started)
[![Works offline](https://img.shields.io/badge/works-offline-5fc98d?style=flat-square)](#-getting-started)
[![GitHub](https://img.shields.io/badge/GitHub-mrz--ir%2FOpenMCU--Cube-181717?style=flat-square&logo=github)](https://github.com/mrz-ir/OpenMCU-Cube)

*Unofficial, unaffiliated tool — not affiliated with Puya Semiconductor or Hangshun (HK).*

</div>

---

## 📑 Table of contents

- [✨ Features](#-features)
- [🎛 Supported devices](#-supported-devices)
- [🚀 Getting started](#-getting-started)
- [📖 Usage guide](#-usage-guide)
- [🗂 Repository layout](#-repository-layout)
- [🛠 Development](#-development)
- [🤝 Contributing](#-contributing)
- [🔒 Security](#-security)
- [⚠️ Disclaimer](#️-disclaimer)
- [📄 License](#-license)

---

## ✨ Features

| | |
|---|---|
| 🧩 **Pinout & Configuration** | Interactive package view with click-to-configure pins: GPIO input/output, alternate functions, analog roles, conflict detection and suggested-pin highlighting. |
| ⏱ **Clock Configuration** | Enable HSI / HSE / LSI / LSE / PLL, choose the SYSCLK source and AHB/APB prescalers, with a **live block diagram** that highlights the active path and warns when a limit is exceeded. |
| 📟 **Code Generation** | HAL-style code for PY32 devices, StdPeriph-style code for HK devices. Download `main.c` or a full ZIP (drivers + startup + **ready-to-open Keil uVision project**). |
| 🔄 **Project restore** | Upload a previous `main.c` to keep your `USER CODE` blocks, or add `project_settings.json` to restore pins, peripherals, clock settings and the project name. |
| 🌗 **Light / Dark theme** | One-click theme toggle, remembered between sessions. |
| ❓ **Inline help** | `?` buttons explain every section on hover. |
| 📱 **Responsive** | Works on phones, tablets and desktops. |
| 🔌 **Zero dependencies** | A single `.html` file that runs fully offline in any modern browser. |

![Clock configuration — dark theme](docs/screenshot-clock-dark.png)

![Inline help tooltip — dark theme](docs/screenshot-tooltip.png)

---

## 🎛 Supported devices

| Family | Packages | Core clock limit |
|--------|----------|------------------|
| **PY32F030** (Puya) | TSSOP20, QFN20, LQFP32 | 48 MHz |
| **PY32F002A** (Puya) | QFN16 | 24 MHz |
| **PY32F002B** (Puya) | QFN20, TSSOP20, SOP14 | 24 MHz |
| **HK32F030M** (Hangshun) | TSSOP20, QFN20 | 32 MHz |
| **HK32F0301M** (Hangshun) | TSSOP20, QFN20, … | 48 MHz |
| **HK32C030** (Hangshun) | QFN32, … | 64 MHz |
| **HK32F103** (Hangshun) | LQFP48 | 120 MHz |

Generated code targets the matching vendor SDK for each family:
**PY32F0xx / PY32F002B HAL** drivers, or the official **HK32 StdPeriph** libraries
(`hk32f103xxxxa` / `hk32c030xx` / `hk32f030m`).

![Pinout configuration — light theme](docs/screenshot-pinout-light.png)

---

## 🚀 Getting started

1. **Download** [`OpenMCU_Cube1_5_7.html`](OpenMCU_Cube1_5_7.html) (or the latest release from the [Releases](https://github.com/mrz-ir/OpenMCU-Cube/releases) page).
2. **Double-click it** — it opens in your browser. No installation, no internet connection needed.
3. Pick your **Device** in the top bar, configure **pins → clocks**, then generate **code**.

> 💡 **Tip:** enable **GitHub Pages** on the repository (Settings → Pages → branch `main`) and the app is also usable online at
> `https://mrz-ir.github.io/OpenMCU-Cube/OpenMCU_Cube1_5_7.html`.

![Mobile layout](docs/screenshot-mobile.png)

---

## 📖 Usage guide

### Tab 1 — Pinout & Configuration
- Click any **pin** on the package drawing to select it.
- Configure its **mode** (GPIO Input / Output / Alt. Function / Analog), pull, output type, or the specific **alternate function**.
- **Ring legend:** <span style="color:#33d6c0">● cyan</span> = assigned · <span style="color:#f2a341">⭘ orange dashed</span> = suggested free pin · <span style="color:#8a6a2a">⚡ amber</span> = clock pin (HSE/LSE) · <span style="color:#e2596b">● blinking red</span> = busy/conflicted.
- In **Enable & Configure Peripherals**, enable a peripheral (USART, SPI, I2C, timers, ADC, CAN…), press **Mark Pins** to see suggested pins, then assign them with one click.

### Tab 2 — Clock Configuration
- Toggle the oscillators (**HSI / HSE / LSI / LSE / PLL**) and set crystal frequencies.
- Choose the **SYSCLK source** and the **AHB / APB prescalers**.
- The computed clock list and the block diagram update live — a block turns **red** when a value exceeds the series limit.

### Tab 3 — Code Generation
- Review the generated `main.c`, then **Copy**, **Download main.c**, or **Download all files (ZIP)**.
- The ZIP includes the driver sources/headers, a startup file and an optional **Keil uVision `.uvprojx`** project with include paths pre-configured.
- **Keep your custom code inside `USER CODE BEGIN/END`** so it survives regeneration.

### Continuing a previous project
1. Open the **Code Generation** tab and select files from the previous project's ZIP *together*:
   - the previous `main.c` (your `USER CODE` blocks are detected and preserved), and
   - `project_settings.json` (restores MCU/package, pins, peripherals, clock tree and project name).
2. Everything is applied to the UI automatically — continue where you left off.

---

## 🗂 Repository layout

```
OpenMCU_Cube1_5_7.html   ← the entire application (single self-contained file)
docs/                    ← screenshots used in this README
LICENSE                  ← MIT license
CHANGELOG.md             ← version history (maintained from v1.5.7 on)
CONTRIBUTING.md          ← how to contribute
SECURITY.md              ← how to report vulnerabilities
```

---

## 🛠 Development

The whole application is **one self-contained HTML file** (HTML + CSS + JavaScript, including the embedded vendor SDK payloads). Working on it is intentionally simple:

1. Open `OpenMCU_Cube1_5_7.html` in any text editor.
2. Edit, then refresh the browser tab to test — no build step.
3. Before committing:
   - bump the version (badge in the top bar, document title, generated-code headers);
   - add a `CHANGELOG.md` entry;
   - re-test the **Clock Configuration** diagram across all supported devices.

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

---

## 🤝 Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) first — it covers how to open issues, submit pull requests, and where the device databases live inside the file.

---

## 🔒 Security

Found a bug that could break a project or compromise a device? Please report it responsibly — see [SECURITY.md](SECURITY.md).

---

## ⚠️ Disclaimer

OpenMCU Cube is an **unofficial, unaffiliated open-source tool**. It is not endorsed by or connected with **Puya Semiconductor** or **Hangshun Microelectronics (HK)**.

Pin maps, alternate-function (AF) tables and clock-tree structures are based on the official datasheets for **PY32F030 / PY32F002A / PY32F002B** (Puya) and **HK32F030M / HK32F0301M / HK32C030 / HK32F103** (Hangshun). They are provided for convenience only — **always verify against the latest official datasheet and reference manual of your exact chip** before manufacturing or releasing firmware.

---

## 📄 License

[MIT](LICENSE) © 2026 MRZelec — OpenMCU Cube contributors
