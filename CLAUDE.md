# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a ZMK firmware configuration for a Corne 42-key split keyboard (nice!nano v2 controller). The keymap defines 6 layers with custom macros, combos, tap dances, and mouse support.

## Key Files

- `config/corne.keymap` - Main ZMK keymap configuration with all layers, macros, behaviors, and combos
- `config/corne.conf` - Optional configuration settings (currently mostly commented out)
- `config/west.yml` - West manifest defining ZMK dependency from zmkfirmware/zmk@main
- `build.yaml` - GitHub Actions build matrix defining left/right shield builds for nice_nano_v2
- `.github/workflows/build.yml` - GitHub Actions workflow that builds firmware on push/PR using ZMK's reusable workflow

## Build Process

The firmware builds automatically via GitHub Actions on every push. The workflow:
1. Uses the reusable workflow from `zmkfirmware/zmk/.github/workflows/build-user-config.yml@main`
2. Builds based on the matrix in `build.yaml` (corne_left and corne_right for nice_nano_v2)
3. Produces firmware artifacts that can be flashed to the keyboard

There is no local build command - all builds happen through GitHub Actions.

## Keymap Architecture

### Layer Structure (6 layers, 0-5)

- **Layer 0 (base)**: QWERTY base layer with home row modifiers on thumb keys
- **Layer 1 (symbols)**: Symbols, brackets, special characters, and Alt+number shortcuts
- **Layer 2 (nav)**: Navigation arrows, Cmd/Ctrl shortcuts, fold/expand macros, scrolling
- **Layer 3 (numbers)**: Number pad layout with arithmetic operators
- **Layer 4 (function)**: Function keys, macros, Cmd shortcuts, and special commands
- **Layer 5 (bt)**: Bluetooth management, mouse movement, function keys, bootloader access

### Key Architectural Components

**Macros** (lines 11-231): Extensive collection including:
- `m0-m2`: Bracket pair macros that insert pairs and position cursor inside
- `m3`: Terminal launcher (Cmd+Ctrl+Alt+T, wait, Alt+3)
- `m4-m8`: Various application and IDE shortcuts
- `m9`: Vim exit macro (ESC, ZZ)
- `m10-m15, m20`: Specialized shortcuts and text insertion
- Legacy named macros: `term`, `foldall`, `fold`, `expand`, `exitvi`, `thisisunsafe`, `bruno`

**Behaviors** (lines 233-264):
- `td10`: Tap dance for mouse button 5 with triple-tap to m10 macro
- `gui5` / `alt5`: Tap dances that switch to layer 5 on triple tap (400ms timing)
- `mouse`: Mouse key press behavior for button clicks

**Combos** (lines 266-356): 18 combos with 150ms timeout including:
- Punctuation shortcuts (semicolon, pipe, slash, backslash, dash, underscore, plus, equals)
- Layer access combos (mo2, mo3, mo4, sl4, to2, to3)
- Mouse click combos (positions 0+12, 12+24)

### Physical Layout Notes

**Corne 42-key positions (0-41)**:
```
 0  1  2  3  4  5       6  7  8  9 10 11
12 13 14 15 16 17      18 19 20 21 22 23
24 25 26 27 28 29      30 31 32 33 34 35
         36 37 38      39 40 41
```

Thumb cluster (positions 36-41) is critical:
- Left thumb: GUI/layer-5-tap, layer-2-tap-space, Shift
- Right thumb: Ctrl, layer-1-tap-enter, Alt/layer-5-tap

## ZMK Documentation

When working with ZMK features, always reference the official documentation at https://zmk.dev/docs/:
- Keymaps: https://zmk.dev/docs/keymaps
- Behaviors: https://zmk.dev/docs/keymaps/behaviors
- Combos: https://zmk.dev/docs/keymaps/combos
- Macros: https://zmk.dev/docs/keymaps/behaviors/macros
- Mouse Keys: https://zmk.dev/docs/keymaps/behaviors/mouse-emulation

## Keymap Editing Guidelines

1. **Preserve ASCII diagrams**: Each layer has a visual layout comment - update it when changing bindings
2. **Column alignment**: Keep binding arrays visually aligned for readability
3. **Consistent naming**: Macros follow m0-m20 pattern, legacy macros use descriptive names
4. **Test builds**: Changes are validated through GitHub Actions - watch for build errors
5. **Mouse features**: This keymap uses mouse emulation (buttons, movement, scrolling)

## Common ZMK Syntax

- `&kp KEY` - Key press
- `&lt LAYER KEY` - Layer-tap (hold for layer, tap for key)
- `&to LAYER` - Switch to layer
- `&mo LAYER` - Momentary layer (active while held)
- `&sl LAYER` - Sticky layer (stays active for next keypress)
- `&mouse BUTTON` - Mouse button (LCLK, RCLK, MB4, MB5)
- `&mmv DIRECTION` - Mouse move (MOVE_UP, MOVE_DOWN, MOVE_LEFT, MOVE_RIGHT)
- `&msc DIRECTION` - Mouse scroll (SCRL_UP, SCRL_DOWN, SCRL_LEFT, SCRL_RIGHT)
- `&kp LC(KEY)` - Ctrl+key
- `&kp LG(KEY)` - Cmd/GUI+key (Mac)
- `&kp LA(KEY)` - Alt+key
- `&kp LS(KEY)` - Shift+key
- `&bt BT_SEL N` - Select Bluetooth profile N
- `&bt BT_CLR` - Clear current Bluetooth profile
- `&bootloader` - Enter bootloader mode for flashing

## Related Project

The `/Users/akiva/personal-projects/zmk-config/` directory contains a similar ZMK configuration for a Sofle keyboard with detailed porting documentation from Vial format. That CLAUDE.md contains valuable general ZMK knowledge but is specific to a different physical layout.
