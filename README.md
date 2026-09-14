# zmk-corne

ZMK firmware configuration for a 42-key Corne split keyboard on nice!nano v2 controllers.

Visual keymap editor: <https://nickcoutsos.github.io/keymap-editor/>

> **Status:** this repo describes the layout **as it is today**. A planned migration to a 36-key layout is documented in [`32-keys.md`](32-keys.md) — no part of it has been applied to `config/corne.keymap` yet.

---

## Repository layout

| Path | What it is |
|---|---|
| `config/corne.keymap` | The whole layout — 6 layers, 29 macro definitions, 4 behaviors, 32 combos |
| `config/corne.conf` | Kconfig flags (Studio, mouse, sleep, BLE power, debounce) |
| `config/west.yml` | West manifest pinning ZMK to `zmkfirmware/zmk@main` |
| `build.yaml` | Build matrix: `corne_left`, `corne_right`, `settings_reset`, all on `nice_nano_v2` |
| `.github/workflows/build.yml` | Calls ZMK's reusable `build-user-config.yml` |
| `32-keys.md` | The planned 36-key transition (English) |
| `32-keys.he.md` | Same plan, Hebrew |
| `akiva.vil` | Vial export for the **Ximi2**, a second keyboard — see below |

There is no local build. Every push builds through GitHub Actions and produces flashable artifacts.

---

## Hardware and physical shape

Corne 42 keys: three rows of six columns per half, three thumb keys per half.

**Left half**

```
⇥  q  w  e  r  t
␛  a  s  d  f  g
`  z  x  c  v  b
         ⌘  ␣  ⇧
```

**Right half**

```
y  u  i  o  p  🖱5
h  j  k  l  ⌫  🖱4
n  m  ,  .  /  ~
⌃  ⏎  ⌥
```

The outer pinky column on each half is the part the planned migration removes.

---

## Layer structure

Six layers. Only five declare a `display-name`; the Bluetooth layer does not, so ZMK Studio shows it under its raw node name.

### Layer 0 — base

QWERTY. **All modifiers live on the thumbs** — there are no home row mods anywhere in this keymap, despite what older notes claim. Three of the six thumb keys are tap-dances rather than plain keys.

| Thumb | Binding | Notes |
|---|---|---|
| Left outer | `&gui5` | Cmd; triple-tap → layer 5. **400 ms** tapping term |
| Left middle | `&lt 2 SPACE` | Space / hold for nav |
| Left inner | `&kp LSHFT` | The only plain-`&kp` modifier on the board |
| Right inner | `&control_record` | Ctrl; double-tap → `Ctrl+Alt+Cmd+\`. No tapping term set (ZMK default) |
| Right middle | `&lt 1 ENTER` | Enter / hold for symbols |
| Right outer | `&alt5` | Alt; triple-tap → layer 5. **400 ms** tapping term |

Punctuation missing from base entirely — `-` `=` `;` `'` `[` `]` `\` — all of it is reached through combos or layers. Backspace exists only on the right pinky home position.

### Layer 1 — symbols

Left hand: the three bracket-pair macros stacked vertically (`{}`, `()`, `[]`, each leaving the cursor inside), plus quotes, colons, and the shifted number row.

Right hand: **Alt+digit** for all ten digits — an application/window switcher. The ASCII comment in the file labels this block as Cmd+digit, which is wrong. Also word- and paragraph-motion, a terminal launcher, and two browser space-switchers.

### Layer 2 — nav

Left hand: Cmd+1..3 and Ctrl+1..5, screen-capture and terminal chords, F4 / Shift+F4.
Right hand: an inverted-T arrow cluster with Home/End/PgUp/PgDn/Delete, the three VS Code fold macros, and word-wise delete.

### Layer 3 — numbers

**Calculator-style numpad** on the right (7-8-9 on the top row, ascending upward), with `0` and `.` on the right thumbs. Operators `/ + -` run down the inner column; `*` and `_` are exiled to the outer column, breaking that family.

The left hand duplicates digits 1–5 that already exist on the right, and the entire lower-left row is `&none`. This is the emptiest layer in the keymap.

### Layer 4 — function

F-keys, screenshot and window-management chords, a vim-exit macro, an email-address macro, the Chrome certificate bypass (bound twice), Caps Lock, and `&caps_word` on a thumb. The only layer with zero `&none`.

### Layer 5 — bt

Bluetooth profile select and disconnect laid out as a grid — row picks the verb, column picks the profile — plus `&bootloader`, `&soft_off`, `&studio_unlock`, F1–F9, and the mouse-move and scroll cluster.

---

## Macros

29 definitions. **Twelve are never referenced**, and nine of those are byte-for-byte duplicates of a macro that is:

| Dead macro | Identical to |
|---|---|
| `m5`, `m6`, `m7` | `fold`, `expand`, `foldall` |
| `m13`, `m14` | inlined as raw `&kp` on two layers |
| `exitvi` | `m9` |
| `arc1`, `arc2` | `m11`, `m12` |
| `parent`, `brackets` | `m1`, `m2` |
| `m3` | `term` (only the internal wait differs) |
| `bruno` | the first tap of `m4` |

Live macros worth knowing: `m15` types an email address (its comment says "kubiya text"), `thinkhard` types a 24-character LLM prompt suffix, `m20` toggles the VS Code terminal and re-runs the last shell command, and `thisisunsafe` types the Chrome SSL bypass phrase.

Two macro **names are inverted**: `fold` binds `Cmd+K Cmd+[`, which is VS Code's *unfold recursively*; `expand` binds `Cmd+K Cmd+]`, which is *fold recursively*.

---

## Behaviors

Four tap-dances, no hold-taps, no mod-morphs, no `&mt` anywhere.

- **`td10`** — mouse button 5 / nothing / lock screen. The deliberate empty middle slot makes the triple-tap lock harder to hit by accident.
- **`gui5`, `alt5`** — Cmd and Alt, each with an identical first and second slot, so double-tapping does nothing; only the triple-tap (→ layer 5) differs. 400 ms.
- **`control_record`** — Ctrl / the record hotkey. No explicit tapping term.

`&lt` and `&mt` are never overridden. Searching the whole file finds **no** `require-prior-idle-ms`, `quick-tap-ms`, `hold-trigger-key-positions`, `flavor`, or `retro-tap` — the layer-taps have no false-positive protection of any kind.

---

## Combos

32 combos, all at a 150 ms timeout, **none scoped with `layers`**, so every one fires on all six layers.

**Punctuation** — the mnemonic core of the layout, and the part that works best:

| Combo | Output | Logic |
|---|---|---|
| `d`+`r` | `/` | traces the glyph, lower-left to upper-right |
| `e`+`f` | `\` | traces the glyph, upper-left to lower-right |
| `s`+`f` | `-` | |
| `x`+`v` | `_` | directly below dash, same two columns |
| `t`+`g` | `;` | vertical, outer column |
| `g`+`b` | `\|` | vertical, outer column |
| `j`+`l` | `+` | |
| `m`+`.` | `=` | directly below plus, same two columns |

Notice that none of these sit on two adjacent columns of the same hand — they skip a column or run vertically, which is what keeps a typing roll from firing them. The two diagonals are the exception.

**Layer access** — `s`+`d`+`f` for the numpad, `s`+`e`+`f` for nav, and adding `a` converts either to a sticky switch. `j`+`k`+`l` and `m`+`,`+`.` both give a sticky function layer.

**Bluetooth** — ten combos anchored on the top-right pinky key, forming a grid where the row picks select-vs-disconnect and the column picks the profile, escalating to clear-one and clear-all.

**Everything else** — left and right click, square brackets, scroll up/down, the prose macro, and a two-key jump to layer 5.

---

## Configuration

Active in `corne.conf`: ZMK Studio, mouse emulation, soft-off, experimental BLE features, sleep with a 15-minute idle timeout, split battery reporting, +8 dBm TX power, and an aggressive 1 ms press debounce (default is 5). RGB underglow and display are commented out.

---

## Known issues in the current layout

Catalogued during an audit; none are fixed yet.

**Correctness**
- The ASCII diagrams disagree with the real bindings in roughly thirty places — an entire layer-1 block is mislabelled Cmd when it binds Alt, and the layer-3 thumb row comment is wholly wrong.
- `scroll-right` is bound on two adjacent keys while `scroll-left` appears nowhere in the file.
- `thisisunsafe` is bound twice on the function layer; several `&to 0` keys are triplicated per layer.

**Latency**
- Base-layer Ctrl, Cmd and Alt are all tap-dances, so every modifier chord waits out a tapping term. Cmd and Alt wait **400 ms** — and those are the two this user's zellij config leans on hardest (~20 Alt bindings, 5 Cmd, with Ctrl used almost only for mode entry), so the largest penalty sits on the most frequent chords.

**Misfire risk**
- The dash combo `s`+`f` is a strict subset of all four layer-access combos, so a slow roll emits `-` instead of switching layers. The 1 ms debounce widens the window.
- The scroll-down combo `a`+`d` overlaps the same family.
- `&sl` releases after **60 seconds**, so an accidental sticky-layer roll arms the function layer for a full minute.
- Layer 5 holds `&bootloader` and `&soft_off` and has four unguarded entrances, one of which is a bare two-key combo.
- No combo is scoped to a layer, so the 24-character prose macro can fire while entering numbers.

---

## The second keyboard

`akiva.vil` is a Vial export for a **Ximi2** used at work — QMK rather than ZMK, already fitted with a trackpad. It runs the same six layers, the same punctuation and layer-access combos, and the same outer-column contents as the Corne, plus a four-key extra cluster per half carrying window-management chords, a record hotkey, browser refresh and hyper shortcuts.

It has its own cruft: four macros are exact duplicates of earlier ones, one macro slot is empty, the nav layer binds the same window shortcut twice, and the numpad's top row has a duplicated `9` where another key belongs.

It is also **ahead of the Corne** in three places, having already moved bindings into the 36-key core that the Corne still anchors on outer-column keys: `q`+`s` and `a`+`w` for square brackets, and `y`+`i`+`p` for layer-5 access. (Its *sticky* layer-5 combo is slated for deletion rather than adoption — a sticky entrance to the layer holding `&bootloader` and `&soft_off` is the one shape that layer should never have.)

Both keyboards are trained by the same hands, so the migration plan treats them as one system.

---

## Planned transition

Full plan: [`32-keys.md`](32-keys.md).

The goal is a **beekeeb Toucan2, 36-key build** — a Cantor/Piantor layout with a multi-touch trackpad, which is this Corne minus the two outer pinky columns. All six thumb keys survive.

The approach is deliberately gradual, and the hardware is bought **last**, only after 36 keys already feels normal on the boards already owned:

1. **Make both maps honest** — fix the diagrams, delete the dead and duplicated macros. No key changes.
2. **Let the trackpads take the pointer** — delete mouse movement, scroll and clicks. Browser back and forward survive; no trackpad maps those.
3. **Take your hands off the brake** — plain modifiers instead of tap-dances, sane sticky-layer timeout, per-layer combo scoping, layer-tap protection.
4. **Converge the two keyboards** — make the 30-key core identical on Corne and Ximi2, adopting the Ximi2's core-only combos.
5. **Two homes for every orphan** — new locations go live while the old keys still work, so the hands drift to whichever is cheaper.
6. **Retire the right column** — nearly free after step 2.
7. **Retire the left column** — Tab, Escape and backtick; the real test, and the only gate on buying hardware.
8. **Order the Toucan2.**

Home row mods are an optional side quest that can run at any point after step 3 and deliberately gates nothing.

Two rules run through the whole plan: never advance a stage on a schedule, only when the previous one is genuinely settled; and never advance on one keyboard alone, because divergence between the two is the likeliest way the migration stalls.

---

## ZMK reference

- Keymaps — <https://zmk.dev/docs/keymaps>
- Behaviors — <https://zmk.dev/docs/keymaps/behaviors>
- Combos — <https://zmk.dev/docs/keymaps/combos>
- Macros — <https://zmk.dev/docs/keymaps/behaviors/macros>
- Mouse keys — <https://zmk.dev/docs/keymaps/behaviors/mouse-emulation>
