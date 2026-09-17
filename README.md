# zmk-corne

ZMK firmware configuration for a 42-key Corne split keyboard on nice!nano v2 controllers, plus the Vial export for the second keyboard the same hands train on.

Visual keymap editor: <https://nickcoutsos.github.io/keymap-editor/>

> **Status:** the 36-key migration documented in [`32-keys.md`](32-keys.md) **has started — on the Ximi2 only.** `akiva.vil` has retired the right outer column on every layer, dropped mouse movement and scroll, moved backtick onto the old `/` key, and rebuilt layer access into a uniform momentary/locked pair per layer. `config/corne.keymap` is **unchanged**: every Corne section below, known issues included, still describes the live layout.
>
> This inverts the plan's own rule of never advancing on one keyboard alone. The exception is deliberate. The Ximi2 is the work board, and the practice hours are on the work board — it is the only place where a 36-key habit gets enough repetitions to become automatic inside a reasonable number of weeks. Advancing the Corne in lockstep would have halved the learning rate on both. The cost is divergence, which the plan names as the likeliest way a migration stalls; stage 4 exists to pay it back, and until it runs the two maps are knowingly out of sync.

---

## Repository layout

| Path | What it is |
|---|---|
| `config/corne.keymap` | The whole Corne layout — 6 layers, 29 macro definitions, 4 behaviors, 32 combos |
| `config/corne.conf` | Kconfig flags (Studio, mouse, sleep, BLE power, debounce) |
| `config/west.yml` | West manifest pinning ZMK to `zmkfirmware/zmk@main` |
| `build.yaml` | Build matrix: `corne_left`, `corne_right`, `settings_reset`, all on `nice_nano_v2` |
| `.github/workflows/build.yml` | Calls ZMK's reusable `build-user-config.yml` |
| `32-keys.md` | The 36-key transition plan (English) |
| `32-keys.he.md` | Same plan, Hebrew |
| `akiva.vil` | Vial export for the **Ximi2**, the work keyboard — the board the migration is actually running on |

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

The outer pinky column on each half is the part the migration removes. On the Ximi2 the right one is already gone.

---

## Corne layer structure

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

## Corne macros

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

## Corne behaviors

Four tap-dances, no hold-taps, no mod-morphs, no `&mt` anywhere.

- **`td10`** — mouse button 5 / nothing / lock screen. The deliberate empty middle slot makes the triple-tap lock harder to hit by accident.
- **`gui5`, `alt5`** — Cmd and Alt, each with an identical first and second slot, so double-tapping does nothing; only the triple-tap (→ layer 5) differs. 400 ms.
- **`control_record`** — Ctrl / the record hotkey. No explicit tapping term.

`&lt` and `&mt` are never overridden. Searching the whole file finds **no** `require-prior-idle-ms`, `quick-tap-ms`, `hold-trigger-key-positions`, `flavor`, or `retro-tap` — the layer-taps have no false-positive protection of any kind.

---

## Corne combos

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

Catalogued during an audit; none are fixed yet. All of these are Corne issues — `config/corne.keymap` has not changed.

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

## The second keyboard — Ximi2

`akiva.vil` is a Vial export for a **Ximi2** used at work: QMK rather than ZMK, already fitted with a trackpad, and carrying a four-key extra cluster per half that has no Corne equivalent (window-management chords, a record hotkey, browser refresh, hyper shortcuts). It is the board the migration is running on, and it is now several stages ahead of the Corne.

What changed: the right outer column is dead on every layer except one tenant; mouse movement and scrolling are gone; backtick moved down onto the old `/` key; layer access was rebuilt into a uniform momentary/locked pair; and two structural Vial bugs were found and fixed.

### Layer maps

Vial stores the right half **reversed** in the JSON — array `col0` is the outermost right key. Everything below is in physical left-to-right order.

**Layer 0 — base**

```
 ⇥   q w e r t        y u i o p   M10
 ␛   a s d f g        h j k l ⌫    ·
 ·   z x c v b        n m , . `    ·
        ⌘  ␣/L2  ⇧    ⌃(TD4) ⏎/L1  ⌥
```

Left thumbs: `RGUI`, `LT2(SPACE)`, `LSHIFT`. Right thumbs: `TD(4)` (Ctrl), `LT1(ENTER)`, `LALT`.

Backtick sits on the right pinky bottom, where `/` used to be. `/` is now combo-only (`d`+`r`), and tilde comes free as shifted backtick — one move retires two doomed keys. `M10` (`Ctrl+Cmd+Q`, lock screen) is the sole remaining tenant of the right outer column, deferred to a later phase. The left outer column is still live for `⇥` and `␛`; its bottom key is already dead.

**Layer 1 — symbols / Alt-digit**

```
DF0  {}  :  "  '  $      ⌥⌃←  ⌥7 ⌥8 ⌥9  ⌥⌃→   ·
 ␛   ()  $  %  &  *      TRM  ⌥4 ⌥5 ⌥6   ^    ·
 ?   []  !  @  #  :       ⌥0  ⌥1 ⌥2 ⌥←   ⌥→   ·
```

The high-frequency `⌥←`/`⌥→` pair took the tight adjacent slot on the bottom row, paying with `⌥3`. The low-frequency `⌥⌃←`/`⌥⌃→` became bookends of the top row. `⌥6` never moved — that was the binding constraint the whole redesign was built around.

**Layer 2 — nav**

```
TO0  ⌘1 ⌘2 ⌘3  ·  TO0     foldall HOME  ↑  PgUp  ⇧⌘.   ·
 ␛   ⌃1 ⌃2 ⌃3 ⌃4  ⌃5      fold     ←    ↓   →    ⌥⌫    ·
⌃`   ⇧⌘E M27 ⇧⌘E ⇧F4 F4   expand  END  ⌦  PgDn   ⌃⌦    ·
```

**Layer 3 — numpad**

```
TO0  1 2 3 4  TO0       *  7 8 9  9   ·
DF0  ⌃1 ⌃2 ⌃3 ⌃4  ·     +  4 5 6  ⌫   ·
 ·   ·  ·  ·  ·   ·     -  1 2 3  ·   ·
                            thumbs: ⌥  0  .
```

The duplicated `9` on the pinky is a pre-existing defect, still unfixed.

**Layer 4 — function**

```
TO0  M9  F2  ⌘F12 F12 TO0      F5   F10 F11 ⇧F11  M8    ·
DF0  M20 ⌃⌥⌘S ⇧⌘5 ⌘F  ⇧⌘F      ⇧⌘F5 F4  M15 ⇧⌘L   ⇧⌥M   ·
CAPS M23 ⇧⌘4 ⇧⌘C ⌃Ins ⌃⌥⌘V     ⇧F5  M4  M22 M12   M24   ·
```

**Layer 5 — F-keys**

```
TO0  ·  ·  ·  ·  TO0        ·  F7 F8 F9  ·    ·
 ·   ·  ·  ·  ·   ·         ·  F4 F5 F6  F12  ·
 ·   ·  ·  ·  ·   ·        F10 F1 F2 F3  F11  ·
```

The left half is `KC_TRNS`, not `KC_NO` — reserved for the Bluetooth controls a wired work board does not need. Both middle thumbs are `TO(0)`.

### Layer access — uniform momentary/locked pairs

Every layer now follows the same shape: a three-key combo for momentary, the same combo plus the pinky for locked.

| Layer | Momentary | Locked |
|---|---|---|
| 2 nav | `s`+`e`+`f` | `a`+`s`+`e`+`f` |
| 3 numpad | `s`+`d`+`f` | `a`+`s`+`d`+`f` |
| 4 function | `j`+`k`+`l` and `m`+`,`+`.`, both `OSL` | none, by choice |
| 5 F-keys | `x`+`c`+`v` | `z`+`x`+`c`+`v` |

Layer 2 is additionally a hold on the left-middle thumb. `TO(0)` is prepared on the inner-index top key (the `t` position) on layers 2, 3, 4 and 5 — that column survives the left-column retirement, so the escape hatch is already in its final home.

### Punctuation combos

Unchanged from the Corne, and the part that works:

| Combo | Output | Combo | Output |
|---|---|---|---|
| `d`+`r` | `/` | `t`+`g` | `;` |
| `f`+`e` | `\` | `g`+`b` | `\|` |
| `f`+`s` | `-` | `q`+`s` | `[` |
| `x`+`v` | `_` | `a`+`w` | `]` |
| `j`+`l` | `+` | `m`+`.` | `=` |

### Pointer combos

Mouse movement and scroll are deleted — the trackpad owns the pointer. Four pointer combos survive because no trackpad maps them:

| Combo | Output | Why it stays |
|---|---|---|
| `i`+`p` | `BTN5` | browser forward |
| `k`+`⌫` | `BTN4` | browser back |
| `q`+`d` | `BTN1` | left click without leaving home row |
| `a`+`c` | `BTN2` | right click without leaving home row |

`⇥`+`b` fires the prose macro.

---

## Lessons learned — two Vial mechanics

Both cost a debugging round, and both are the kind of failure that produces no error and no log line. They are the most reusable output of the session.

**1. Combos match resolved keycodes, not key positions.**

A Vial combo is defined as a set of keycodes, and it fires when the keys currently producing those keycodes are pressed together. It does not reference physical positions. So wrapping a base-layer key in `TD(n)` changes what that position resolves to, and **silently removes it from every combo that names the underlying keycode**. The combo stays in the file, looks correct in the editor, and never fires again.

This happened twice. Putting `.` behind a tap dance killed `m`+`.` → `=`, and `=` existed nowhere else in the layout, so the character became untypeable. The same mechanism had already killed `ESC`+`` ` `` when backtick moved behind a dance.

Corollary, and the rule to keep: **a combo whose trigger keycode is absent from the base layer is inert.** Any key move, any new tap dance, any layer reshuffle can orphan a combo. Check the combo list against the base layer after every one of them.

**2. Vial's double-tap slot replaces both taps, it does not add a second one.**

The tap-dance slots are tap / hold / double-tap / tap-hold. Setting the double-tap slot to the same keycode as the tap slot does not give you two characters — it gives you one, because the double-tap action *substitutes for* the pair of taps rather than following them. Leave the double-tap slot as `KC_NO` to get the natural behaviour of the tap keycode emitted twice.

This broke ```` ``` ```` on the backtick key: three backticks came out as fewer than three, unpredictably, depending on typing speed.

---

## Accepted costs

These are decisions, not defects. Each was taken knowingly and each has a reason.

**Direction reversal on the bottom-row pinky.** That key was `⌥⌃←` and is now `⌥→`. Re-learning a reversed direction on one key is the price of putting the high-frequency `⌥←`/`⌥→` pair on adjacent keys, and the low-frequency pair got the bookend positions instead.

**Combo subset overlaps.** Thirteen subset relationships exist among the twenty-three live combos — a shorter combo whose keys are all contained in a longer one. QMK defers to the longer combo for as long as it is still reachable, so the shorter one only escapes when the last key of the longer chord lands after `COMBO_TERM`. That makes this a timing bug under slow rolls, not an always-on collision. Explicitly accepted: *"I am not afraid of combo overlaps."* The notable cases:

| Shorter | Contained in |
|---|---|
| `f`+`s` → `-` | all four home-row layer combos |
| `f`+`e` → `\` | both nav combos |
| `j`+`l` → `+` | `j`+`k`+`l` |
| `m`+`.` → `=` | `m`+`,`+`.` |
| `x`+`v` → `_` | both layer-5 combos |

Note that the fix chosen here was the mirror image of the one `32-keys.md` proposed: the *layer combo* moved, not the punctuation.

**`td[4]` stays a tap dance.** `[tap=LCTRL, hold=LCTRL, double=LCAG(\), 210 ms]`. Stage 3 says replace tap-dance modifiers with plain ones, and this one is declined on purpose: the Corne has no spare key for the record hotkey, so keeping the double-Ctrl habit on the Ximi2 is what lets it transfer.

**Layer 5's left half stays `KC_TRNS`.** Reserved for the Bluetooth controls the Corne needs and the work board does not. The consequence is real and accepted: while locked into layer 5, every left-hand combo is still armed.

**Dropped and not missed:** `⌥↑`/`⌥↓`, `⌥3`, layer-4 `F8` and `F9`, layer-3's left-half `5`, `TO(4)`, and the `q`+`a`+`z` sticky layer-5 combo.

---

## Ximi2 backlog

Open, known, and deliberately not fixed yet. Twenty-three combos are live (`UI1`-`UI7`, `UI10`-`UI21`, `UI24`, `UI26`, `UI28`, `UI29`); of the other nine slots, all are empty except `UI31`.

- `UI31` is a malformed leftover: no trigger keys at all, but a `KC_BTN1` output keycode still stranded in the slot. Inert, and untidy.
- `td[2]` gates layer 1's Shift behind a 200 ms dance while base-layer Shift is plain — an inconsistency with no justification.
- Ten configured tap-dance slots are unreferenced; only `TD(2)` and `TD(4)` are bound anywhere.
- Orphan macros: `M11`, `M21`, `M25`, `M26`.
- `M12` and `M4` are both `./`.
- Layer 3's duplicate `9`.
- Layer 2 binds `SGUI(E)` twice.
- The extra cluster is undrained and has no Toucan2 equivalent; everything on it eventually has to move or die.
- The Corne keymap bindings are unported — deliberately, until the work board settles.

---

## Planned transition

Full plan: [`32-keys.md`](32-keys.md).

The goal is a **beekeeb Toucan2, 36-key build** — a Cantor/Piantor layout with a multi-touch trackpad, which is this Corne minus the two outer pinky columns. All six thumb keys survive.

The approach is deliberately gradual, and the hardware is bought **last**, only after 36 keys already feels normal on the boards already owned.

| # | Stage | Corne | Ximi2 |
|---|---|---|---|
| 1 | **Make both maps honest** — fix diagrams, delete dead and duplicated macros. No key changes | not started | partial — mouse cruft gone, but orphan macros, the duplicate `9` and the double-bound `SGUI(E)` remain |
| 2 | **Let the trackpads take the pointer** — delete movement, scroll and clicks; keep browser back/forward | not started | done — movement and scroll deleted, four pointer combos kept by choice |
| 3 | **Take your hands off the brake** — plain modifiers, sane sticky timeout, per-layer combo scoping, layer-tap protection | not started | partly declined — `td[4]` kept for Corne parity, `td[2]` still gating layer-1 Shift, combos still unscoped |
| 4 | **Converge the two keyboards** — identical 30-key core on both | not started | blocked on the Corne; this is the stage that repays the divergence |
| 5 | **Two homes for every orphan** — new locations go live while the old keys still work | not started | done for `/`, backtick and the square brackets |
| 6 | **Retire the right column** — nearly free after stage 2 | not started | done except `M10` (lock screen), which is deferred |
| 7 | **Retire the left column** — Tab, Escape and backtick; the real test, and the only gate on buying hardware | not started | started — the outer bottom key is dead and the `TO(0)` escape hatch is pre-placed on the `t` column |
| 8 | **Order the Toucan2** | blocked on stage 7, both boards | |

Home row mods are an optional side quest that can run at any point after stage 3 and deliberately gates nothing.

Two rules run through the whole plan: never advance a stage on a schedule, only when the previous one is genuinely settled; and never advance on one keyboard alone, because divergence between the two is the likeliest way the migration stalls. The second rule is currently suspended, for the reason given in the status note at the top, and stage 4 is where the bill comes due.

---

## ZMK reference

- Keymaps — <https://zmk.dev/docs/keymaps>
- Behaviors — <https://zmk.dev/docs/keymaps/behaviors>
- Combos — <https://zmk.dev/docs/keymaps/combos>
- Macros — <https://zmk.dev/docs/keymaps/behaviors/macros>
- Mouse keys — <https://zmk.dev/docs/keymaps/behaviors/mouse-emulation>
