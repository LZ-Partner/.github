# Repository & Version Naming Conventions

This document defines **standard naming conventions** for repositories, tags, releases, branches, and shared libraries used across the organization.

It is designed to be stored in the **`.github`** organization repository and referenced by all engineers.

---

## 0) Global rules (apply everywhere)

### 0.1 Case + separators
- Use **lowercase** for repository names.
- Use **kebab-case** (`-`) as the separator.
- Avoid spaces and underscores (`_`).

Good: `product-if-line-controller-hw`  
Bad: `if_line_controller_HW`, `IF Line Controller`

### 0.2 Prefixes (group by purpose)
Use consistent prefixes so GitHub search and repository lists stay navigable as the company scales.

- `product-…` : shipping products (hardware/firmware/apps)
- `platform-…` : shared libraries and reusable components
- `tools-…` : internal tooling (manufacturing, calibration, flashing, etc.)
- `infra-…` : CI/CD, templates, automation, runners
- `docs-…` : documentation-only repositories (optional)
- `archive-…` : archived legacy repos (optional)

### 0.3 Product slug
Choose a **single product slug** and use it everywhere.

- Product slug: `if-line-controller`

If you also have internal product codes (recommended later), extend to:
- `product-<code>-<product>-…`  (e.g., `product-lcz-100-if-line-controller-hw`)

---

# 1) Hardware naming conventions (KiCad)

## 1.1 Hardware repository names (single PCB per product)

### Standard
- **`product-<product>-hw`**

Examples:
- `product-if-line-controller-hw`
- `product-ventctrl-hw`

### If you ever split into multiple boards per product (future-proofing)
- **`product-<product>-<board>-hw`**

Examples:
- `product-ventctrl-mainboard-hw`
- `product-ventctrl-sensorboard-hw`

> Note: For early prototyping, avoid creating multiple repos like `…-proto1`, `…-proto2`. Use **tags/releases** to mark manufactured iterations.

---

## 1.2 Hardware versions: **Revision vs Prototype**

### Definitions
- **`revA`, `revB`, …**: design revision (meaningful circuit/layout change)
- **`proto1`, `proto2`, …**: build iteration for that revision (same design, new build/run)

If the next iteration differs strongly, it is typically a **new revision**, not “proto2”.

---

## 1.3 Hardware tags (manufacturing milestones)

Use tags to mark the exact commit sent to fabrication.

### Tag format
- **`hw-rev<LETTER>-proto<N>`**

Examples:
- `hw-revA-proto1`
- `hw-revA-proto2`
- `hw-revB-proto1`

### Optional (later lifecycle)
- `hw-revB-evt1`, `hw-revB-dvt1`, `hw-revB-pvt1`

---

## 1.4 GitHub Releases (hardware)

Create a GitHub Release for each manufacturing tag.

- Release name: **same as the tag** (e.g., `hw-revA-proto1`)
- Attach fabrication assets (Gerbers, BOM, PnP, PDFs) to the Release.

### Release asset naming
Format:
- **`<product>_<tag>_<artifact>.<ext>`**

Examples:
- `if-line-controller_hw-revA-proto1_gerbers.zip`
- `if-line-controller_hw-revA-proto1_drill.zip`
- `if-line-controller_hw-revA-proto1_bom.csv`
- `if-line-controller_hw-revA-proto1_pnp.csv`
- `if-line-controller_hw-revA-proto1_schematic.pdf`

---

## 1.5 Shared KiCad libraries (company-wide)

### Recommended split (best scaling)
1) **Text-friendly library repo (symbols + footprints)**
- **`platform-hw-kicad-lib`**

2) **Binary-heavy 3D models repo (STEP/WRL via Git LFS)**
- **`platform-hw-kicad-3d`**

Examples:
- `platform-hw-kicad-lib`
- `platform-hw-kicad-3d`

### If you prefer fully explicit naming
- `platform-hw-kicad-symbols`
- `platform-hw-kicad-footprints`
- `platform-hw-kicad-step`

---

## 1.6 Submodule naming (hardware)

If product repos consume the shared libraries via submodules, standardize paths:

- `libs/platform-hw-kicad-lib`
- `libs/platform-hw-kicad-3d`

---

## 1.7 Recommended folder layout inside `product-<product>-hw`

Suggested structure:

- `kicad/` : KiCad project files
- `libs/` : submodules
- `docs/` : bring-up notes, requirements, decisions
- `fab/` : generated outputs (should be gitignored)

Example:

```text
product-if-line-controller-hw/
  kicad/
  libs/
  docs/
  fab/        # generated outputs; keep out of Git history
  README.md
  .gitignore
```

---

# 2) Firmware naming conventions

## 2.1 Firmware repository names (what gets flashed)

### Standard
- **`product-<product>-fw`**

Examples:
- `product-if-line-controller-fw`
- `product-ventctrl-fw`

### Optional: MCU-specific naming (only if one product targets multiple MCU families)
- `product-<product>-fw-<mcu>`

Examples:
- `product-ventctrl-fw-stm32l4`
- `product-ventctrl-fw-stm32f4`

---

## 2.2 Bootloader vs application

If you ship both a bootloader and an application image:

### Option A (two repos)
- `product-<product>-boot`
- `product-<product>-fw`

### Option B (one repo, two targets) — preferred unless teams/cadence diverge
- Repo: `product-<product>-fw`
- Folders: `bootloader/` and `app/`

---

## 2.3 Firmware release tags

Firmware should use SemVer-like tags since you ship binaries.

### Tag format
- **`fw-v<MAJOR>.<MINOR>.<PATCH>`**

Examples:
- `fw-v1.0.0`
- `fw-v1.2.3`

### Pre-releases
- `fw-v1.3.0-rc1`
- `fw-v1.3.0-beta1`

---

## 2.4 GitHub Releases (firmware)

Create a GitHub Release for each firmware tag and attach built artifacts.

### Firmware asset naming
Format:
- **`<product>_<tag>_<target>.<ext>`**

Examples:
- `if-line-controller_fw-v1.2.0_app.bin`
- `if-line-controller_fw-v1.2.0_boot.hex`
- `if-line-controller_fw-v1.2.0_app.elf` (optional)

---

## 2.5 Shared firmware repositories (platform code)

Use separate repos for shared components to keep product repos clean and scalable.

### BSP / HAL glue
- **`platform-fw-bsp-stm32`**

(If other MCU vendors appear later: `platform-fw-bsp-nxp`, `platform-fw-bsp-esp32`, …)

### Drivers
- **`platform-fw-drivers`**

Optional split if it grows:
- `platform-fw-drivers-sensors`
- `platform-fw-drivers-peripherals`

### Protocols / messaging
- **`platform-fw-protocols`**

Optional split:
- `platform-fw-protocols-can`
- `platform-fw-protocols-modbus`

### Common utilities / libraries
- **`platform-fw-libs`**

---

## 2.6 FreeRTOS / third-party code

Treat FreeRTOS (and similar) as third-party.

### If you vendor it as its own repository
- **`thirdparty-freertos`**

### If you include it inside a firmware repository
Use a dedicated folder:
- `third_party/freertos/`

---

## 2.7 Firmware branch naming

Keep branch names short and descriptive:

- `main` (stable)
- `feat/<topic>`
- `fix/<topic>`
- `release/<version>` (optional)
- `hotfix/<version>` (optional)

Examples:
- `feat/can-diagnostics`
- `fix/i2c-timeout`
- `release/1.3.0`

---

## 2.8 Hardware ↔ Firmware compatibility mapping

Every firmware repo should contain a simple mapping:

- `COMPATIBILITY.md` (or `support-matrix.yml`)

Reference hardware tags directly:
- `hw-revA-proto1` → supported firmware tags / minimum version

---

# 3) Organization / meta repository naming conventions

## 3.1 Organization-wide GitHub defaults

### GitLab profile equivalent
- **`.github`** (special repository name)

Purpose: store org-wide templates and defaults:
- PR templates
- Issue templates
- CONTRIBUTING guidelines
- Code of Conduct (optional)
- Security policy (optional)
- Shared starter workflows (optional)

---

## 3.2 Engineering handbook / onboarding

- **`handbook-engineering`**

Alternatives (pick one and stick with it):
- `engineering-handbook`
- `dev-handbook`

---

## 3.3 CI/CD and automation

- **`infra-ci`** (shared GitHub Actions workflows, scripts, runners configuration)

---

## 3.4 Tooling repositories

Use the `tools-` prefix for internal tooling:

- `tools-manufacturing-test`
- `tools-calibration`
- `tools-production` (factory tooling)
- `tools-devops` (if needed)

---

## 3.5 Documentation repositories (optional)

If you need standalone documentation repos:

- `docs-internal`
- `docs-product-<product>`

---

## 3.6 Archive repositories (optional)

For legacy repos you want to preserve but stop using:

- `archive-<oldname>`

Also enable GitHub “Archive” (read-only) on the repository.

---

# Appendix: Quick examples

## Example: Hardware repo + tags
- Repo: `product-if-line-controller-hw`
- Manufactured iterations:
  - `hw-revA-proto1`
  - `hw-revB-proto1`

## Example: Firmware repo + tags
- Repo: `product-if-line-controller-fw`
- Firmware releases:
  - `fw-v1.0.0`
  - `fw-v1.1.0`

## Example: Shared platform repos
- `platform-hw-kicad-lib`
- `platform-hw-kicad-3d`
- `platform-fw-bsp-stm32`
- `platform-fw-drivers`
- `platform-fw-protocols`
- `platform-fw-libs`
- `thirdparty-freertos`

