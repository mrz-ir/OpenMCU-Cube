# Security Policy

## Reporting a vulnerability

OpenMCU Cube is a code-generation tool: a bug could produce *plausible-looking but
wrong* pin mappings, clock configurations, or initialization code that could
damage hardware or brick a device if flashed.

If you believe you have found a security-relevant issue, **please do not open a
public issue**. Instead, report it privately:

- Open a [private advisory](https://github.com/mrz-ir/OpenMCU-Cube/security/advisories/new) on GitHub, **or**
- Send a message via the repository's [Security tab](https://github.com/mrz-ir/OpenMCU-Cube/security).

Please include:

1. The **version** and **device/package** affected.
2. A **step-by-step description** of how the wrong output is produced.
3. An example of the **generated code or configuration** that is wrong, and what the correct output should be.
4. (If known) the impact — e.g. "generated clock configuration exceeds the max frequency and would run the MCU out of spec".

## What counts as a vulnerability

- Incorrect clock limits that could push a device out of specification.
- Wrong pin/alternate-function data that could short pins or mis-wire peripherals.
- Anything that silently corrupts the generated project (malformed `.uvprojx`, broken ZIP, …).
- XSS or similar injection risks in the single-file app (e.g. via uploaded filenames or `project_settings.json`).

## What is **not** in scope

- The tool being unofficial / unaffiliated (this is documented in the README).
- Datasheet mistakes that were already correct per the *official* datasheet — but we still want to know, so please report them as normal issues.

## Supported versions

| Version | Supported |
|---------|-----------|
| 1.5.x (latest) | ✅ actively supported |
| Older 1.x | ⚠️ best effort |
| Pre-1.5.4 | ❌ not supported (no longer published) |

## Disclosure

We aim to acknowledge reports within **7 days** and will coordinate a fix and
release before public disclosure where practical.
