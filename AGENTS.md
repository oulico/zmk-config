# AGENTS.md

ZMK firmware **user-config** repository for two split keyboards. There is no application code, no local toolchain, no test suite, and no package manager. Firmware is built exclusively by GitHub Actions; do not attempt `west build` locally.

## Build & deploy

- All firmware images are built by `.github/workflows/build.yml`, which calls ZMK's reusable workflow `zmkfirmware/zmk/.github/workflows/build-user-config.yml@main`. Pushing to any branch triggers a build; artifacts (`*_left.uf2`, `*_right.uf2`) are downloadable from the Actions run.
- The build matrix lives in `build.yaml` at the repo root. To add or remove a firmware image, edit this file. Current matrix: `cygnus_left`, `cygnus_right`, `skeletyl_left`, `skeletyl_right`.
- Board is always `nice_nano//zmk`. The `//zmk` suffix is **not a typo** — it is Zephyr 4.1 board-revision syntax required after the migration in commit `cb29d17`. Do not "fix" it to bare `nice_nano`.
- `config/west.yml` imports `zmkfirmware/zmk` at branch `main`. Bumping or pinning ZMK happens here.
- `zephyr/module.yml` registers this repo as Zephyr module `zmk-keyboard-cygnus` with `board_root: .`, which is what makes the `boards/shields/` directory visible to the build. The module name is historical (predates the Skeletyl addition); do not rename it without verifying the build still discovers shields.

## Repository layout

```
build.yaml                 # build matrix consumed by ZMK's CI workflow
config/west.yml            # west manifest (imports zmkfirmware/zmk@main)
zephyr/module.yml          # Zephyr module registration + board_root
boards/shields/<name>/     # one directory per keyboard shield
  <name>.zmk.yml           # shield metadata (id, siblings, features, requires)
  <name>.keymap            # keymap + behaviors + combos (DTS)
  <name>.dtsi              # shared hardware def: kscan, matrix transform, physical layout
  <name>_layouts.dtsi      # (skeletyl only) physical key layout
  <name>_left.overlay      # left-half GPIO columns + central role
  <name>_right.overlay     # right-half GPIO columns + col-offset = <5>
  <name>_left.conf         # left (central) Kconfig fragment
  <name>_right.conf        # right (peripheral) Kconfig fragment
  Kconfig.shield           # SHIELD_<NAME>_{LEFT,RIGHT} symbols
  Kconfig.defconfig        # ZMK_KEYBOARD_NAME, split role defaults
```

## Conventions that are easy to get wrong

- **Split role is hardcoded to the left half.** `Kconfig.defconfig` sets `ZMK_SPLIT_ROLE_CENTRAL=y` only under `SHIELD_*_LEFT`. The right overlay sets `col-offset = <5>` to address columns 5–9. Swapping handedness means editing both files, not just the overlay.
- **`_<side>.conf` files are loaded automatically** by the build system when the shield is named in `build.yaml`. This was broken and fixed in commit `f6d60bf` — do not duplicate config into a board-level `.conf` to "make sure it applies"; that will double-define symbols.
- **The two keymaps (`cygnus.keymap`, `skeletyl.keymap`) are intentionally byte-identical** — same 7-layer Miryoku layout (BASE/SYM/NAV/NUM/FUN/MOUSE/MEDIA), same `hml`/`hmr` hold-tap homerow-mod behaviors, same combos. When editing one, update the other in the same change unless the task explicitly scopes to a single keyboard.
- **Diode direction differs between the two keyboards.** Cygnus uses `col2row`, Skeletyl uses `row2col`. This is hardware-dependent and set in each shield's `.dtsi`. Do not normalize.
- **Korean comments are intentional.** Keymaps and some configs use Korean inline comments (`// QMK 스타일 레이어 정의`, `// 빈 키 정의`). Preserve this style when editing those files; commit messages also mix Korean and English.

## Adding a new keyboard shield

A new shield requires all of the following in `boards/shields/<name>/` or the build will fail to discover it:

1. `<name>.zmk.yml` — metadata with `siblings: [<name>_left, <name>_right]`.
2. `Kconfig.shield` — `SHIELD_<NAME>_LEFT` / `SHIELD_<NAME>_RIGHT` symbols via `$(shields_list_contains,...)`.
3. `Kconfig.defconfig` — `ZMK_KEYBOARD_NAME`, `ZMK_SPLIT_ROLE_CENTRAL` under the `_LEFT` guard, `ZMK_SPLIT` under the combined guard.
4. `<name>.dtsi` (+ optional `<name>_layouts.dtsi`) — `kscan`, `matrix-transform`, `physical-layout`, and a `chosen` node wiring `zmk,kscan` and `zmk,physical-layout`.
5. `<name>_left.overlay` and `<name>_right.overlay` — per-half `col-gpios`; right side must add `col-offset = <5>` to the transform.
6. `<name>_left.conf` and `<name>_right.conf` — Kconfig fragments. Left (central) sets `CONFIG_BT_MAX_CONN=6`, `CONFIG_ZMK_SPLIT_BLE_CENTRAL_PERIPHERALS=1`, and the battery-proxy symbols; right (peripheral) sets `CONFIG_BT_MAX_CONN=1`.
7. `<name>.keymap` — usually copied from an existing shield and adjusted.
8. Append both halves to `build.yaml`.

## Mouse / pointing

- Both keyboards enable `CONFIG_ZMK_POINTING=y` and define `ZMK_POINTING_DEFAULT_MOVE_VAL 1800` / `ZMK_POINTING_DEFAULT_SCRL_VAL 30` (3× default) directly in the keymap via `#define`, before `#include <dt-bindings/zmk/pointing.h>`. These defines override Kconfig defaults at compile time — do not also set them in `.conf`.
- The mouse layer uses `&mmv` (mouse-move), `&msc` (mouse-scroll), `&mkp` (mouse-click) bindings.

## Branches

- `main` — stable, builds cleanly.
- `miryoku` / `fix/miryoku` — experimental Miryoku-layout work. The current `HEAD` is on `fix/miryoku`, which is ahead of `main`.

## Things this repo does NOT have

- No local `west` workspace, no `build/` artifacts, no Zephyr SDK — everything is delegated to CI. Do not generate lockfiles or run `west update`.
- No tests, linters, formatters, or type checkers. Verification = "did the GitHub Actions build produce a `.uf2`?"
- No `.gitignore` beyond Serena's; the `.serena/` and `.idea/` directories are tooling-only and irrelevant to firmware.
