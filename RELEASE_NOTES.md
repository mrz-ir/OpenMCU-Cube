# OpenMCU Cube — v1.5.7 Release Notes

Use this text directly in the **GitHub Releases** page (paste it into the
“Describe this release” field). Tag: **v1.5.7**

---

## 🧊 OpenMCU Cube v1.5.7

STM32CubeMX-style graphical configurator for **HK32 / PY32** microcontrollers —
pinout, clock tree and code generation, in a **single offline HTML file**.

### ✨ Highlights

- **GPIO Output build fix** — for HK32F030M / HK32F0301M / HK32C030 the generated
  code now uses `GPIO_Mode_OUT` + `GPIO_OType_PP/OD` (the macros these StdPeriph
  drivers actually define) instead of the non-existent `GPIO_Mode_Out_PP/OD`.
  HK32F103 keeps its correct F1-style `GPIO_Mode_Out_PP/OD`. Generated projects
  now build without "undeclared identifier" errors.
- **Clock tree diagram** reworked — no more overlapping labels/lines; wires are
  drawn *under* the boxes; multi-bus devices (HK32F103 APB1/APB2) stack cleanly.
- **Light / Dark theme toggle** (remembered between sessions).
- **Inline help (`?`) buttons** with English tooltips on every section.
- **Tooltips open below** the button so nothing is clipped at the top of the page.
- **Mobile-friendly header** — the logo stays on top of the menu on phones/tablets.
- **Zero dependencies** — runs fully offline in any modern browser.

### 🎛 Supported devices

| Family | Packages | Max clock |
|--------|----------|-----------|
| PY32F030 | TSSOP20, QFN20, LQFP32 | 48 MHz |
| PY32F002A | QFN16 | 24 MHz |
| PY32F002B | QFN20, TSSOP20, SOP14 | 24 MHz |
| HK32F030M | TSSOP20, QFN20 | 32 MHz |
| HK32F0301M | TSSOP20, QFN20, … | 48 MHz |
| HK32C030 | QFN32, … | 64 MHz |
| HK32F103 | LQFP48 | 120 MHz |

### 📦 Downloads

- [OpenMCU_Cube1_5_7.html](https://github.com/mrz-ir/OpenMCU-Cube/releases/download/v1.5.7/OpenMCU_Cube1_5_7.html) — the application (single file, no install)

> 💡 Enable **GitHub Pages** for online use: `https://mrz-ir.github.io/OpenMCU-Cube/OpenMCU_Cube1_5_7.html`

### ⚠️ Disclaimer

Unofficial, unaffiliated tool — not affiliated with Puya Semiconductor or Hangshun
(HK). Always verify against the latest official datasheet and reference manual
before building hardware.

---

*Full details in [CHANGELOG.md](CHANGELOG.md) · [MIT licensed](LICENSE)*
