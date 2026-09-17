# zmk-corne

ZMK firmware configuration for a 42-key Corne split keyboard on nice!nano v2 controllers, plus the Vial export for the second keyboard the same hands train on.

Visual keymap editor: <https://nickcoutsos.github.io/keymap-editor/>

> **Status:** the 36-key migration documented in [`32-keys.md`](32-keys.md) **has caught the Corne up.** `config/corne.keymap` is now a position-for-position port of `akiva.vil`: every binding on all six layers matches the Ximi2, with the Ximi2-only hardware left behind — the outer seventh column, the extra per-half cluster, the rotary encoders and the attached mouse have no representation here.
>
> The divergence the status note used to apologise for is paid off. Stage 4 — converge the two keyboards — is done for the bindings, and stages 1, 2, 3, 5 and 6 came with it. What is left is stage 7, retiring the left outer column, which is the only gate on buying hardware.
>
> The two maps now differ in exactly three places, all of them deliberate: three keys on layer 5 — Studio unlock, soft off and the bootloader — which ZMK needs and QMK has no equivalent for; the layer-tap protection on `&lt`, which QMK has no equivalent knob for either; and Caps Lock, which the Ximi2 reaches from a tap-then-hold on the left thumb and the Corne does not reach at all, because ZMK's tap-dance has no tap-hold slot to port it to. Bluetooth, which used to be the largest difference, now lives entirely in combos and takes up no keymap positions at all.

---

## Repository layout

| Path | What it is |
|---|---|
| `config/corne.keymap` | The whole Corne layout — 6 layers, 25 macro definitions, 1 behavior, 40 combos |
| `config/corne.conf` | Kconfig flags (Studio, pointer buttons, combo limits, sleep, BLE power, debounce) |
| `config/west.yml` | West manifest pinning ZMK to `zmkfirmware/zmk@main` |
| `build.yaml` | Build matrix: `corne_left`, `corne_right`, `settings_reset`, all on `nice_nano_v2` |
| `.github/workflows/build.yml` | Calls ZMK's reusable `build-user-config.yml` |
| `combos.md` | All 40 combos, one diagram each |
| `32-keys.md` | The 36-key transition plan (English) |
| `32-keys.he.md` | Same plan, Hebrew |
| `akiva.vil` | Vial export for the **Ximi2**, the work keyboard — the source of truth for the layout |

There is no local build. Every push builds through GitHub Actions and produces flashable artifacts.

---

## Hardware and physical shape

Corne 42 keys: three rows of six columns per half, three thumb keys per half. Every map below draws both halves together, in physical left-to-right order, with the gap between them standing in for the two controllers.

The outer pinky column on each half is the part the 36-key migration removes, and both boards now agree on what is still standing there — Tab and Escape on the left, the lock-screen macro on the right, ❌ everywhere else. Backtick sits on the right pinky bottom row, where slash used to be; slash is now the `d`+`r` combo and tilde is gone. Backspace is on the right pinky home position. The base layer carries no `-` `=` `;` `'` `[` `]` `\` at all — every one of those is a combo or a layer.

---

## Layer maps

Six layers, each with a `display-name` and **each with its own hue**, so a glance at the colour says which layer you are looking at before you read a single key:

| Layer | Hue | | Layer | Hue |
|---|---|---|---|---|
| 0 base | slate | | 3 numbers | amber |
| 1 symbols | teal | | 4 function | violet |
| 2 nav | green | | 5 F-keys | rose |

Within a layer, the shade says what kind of key it is — lightest to deepest:

| Shade | Meaning |
|---|---|
| lightest | plain unmodified keypress — a letter, a digit, an F-key, an arrow |
| light | a modifier — a chord like `⌘1`, or a thumb modifier |
| mid | a macro — a sequence, not a single chord |
| deepest | the layer's own signature output — the symbols on layer 1, the operators on layer 3, the macros on layer 4 |

Two classes ignore the layer hue, because what they mean does not change between layers: **red** is destructive, irreversible, or a known defect; **dashed grey ❌** is a key with nothing on it — `&none` everywhere, plus layer 5's `&trans` left half, which only shows base through.

One rule ties the hues together: **a key that switches layers wears the colour of the layer it goes to.** That is why layer 0's Space is green and its Enter is teal — holding them is how nav and symbols are reached — and why `TO0`, the escape hatch back to base, is slate on all five of the others.

### Layer 0 — base

QWERTY, all modifiers on the thumbs, no home row mods. Space and Enter wear nav's and symbols' colours because holding them is how you get there.

```mermaid
%%{init: {'theme':'base','themeVariables':{'primaryColor':'#eef1f5','primaryTextColor':'#0b0f14','primaryBorderColor':'#5b6673','nodeTextColor':'#0b0f14','textColor':'#0b0f14','mainBkg':'#eef1f5','fontSize':'18px'}}}%%
block
columns 13

  tab["⇥"] q w e r t space y u i o p lock["🔒"]
  esc["␛"] a s d f g space h j k l bspc["⌫"] x1["❌"]
  x2["❌"] z x c v b space n m cma[","] dot["."] grv[" `"] x3["❌"]
  space:3 cmd["⌘"] spc["␣"] sft["⇧"] space ctl["⌃"] ent["⏎"] alt["⌥"] space:3

  classDef plain fill:#eef1f5,stroke:#5b6673,stroke-width:2px,color:#0b0f14
  classDef mod fill:#dbe3ec,stroke:#3c4a5c,stroke-width:3px,color:#0a1219
  classDef goL1 fill:#8ed7e6,stroke:#053541,stroke-width:4px,color:#02141a
  classDef goL2 fill:#93d9a8,stroke:#063a2c,stroke-width:4px,color:#031410
  classDef alert fill:#ffd5d0,stroke:#8c1008,stroke-width:3px,color:#3d0603
  classDef dead fill:#f2f3f5,stroke:#98a2ae,stroke-width:2px,stroke-dasharray:5 4,color:#5b6673

  class q,w,e,r,t,a,s,d,f,g,z,x,c,v,b,y,u,i,o,p,h,j,k,l,bspc,n,m,cma,dot,grv plain
  class cmd,sft,ctl,alt mod
  class ent goL1
  class spc goL2
  class tab,esc,lock alert
  class x1,x2,x3 dead
```

### Layer 1 — symbols

Bracket-pair macros and the shifted number row on the left; ⌥+digit app switching, word motion and the terminal on the right.

```mermaid
%%{init: {'theme':'base','themeVariables':{'primaryColor':'#e6f6f9','primaryTextColor':'#04222b','primaryBorderColor':'#0a4f63','nodeTextColor':'#04222b','textColor':'#04222b','mainBkg':'#e6f6f9','fontSize':'18px'}}}%%
block
columns 13

  to0["TO0"] cur[" {}"] semi[";"] dqt["#quot;"] sqt["'"] dol["$"] space wl["⌥⌃←"] a7["⌥7"] a8["⌥8"] a9["⌥9"] wr["⌥⌃→"] x1["❌"]
  esc["␛"] par[" ()"] dl2["$"] pct["%"] amp[" &"] ast["*"] space trm["💻"] a4["⌥4"] a5["⌥5"] a6["⌥6"] car["^"] x2["❌"]
  qm["?"] sqb[" []"] exc["!"] at["@"] hsh[" #"] col[":"] space a0["⌥0"] a1["⌥1"] a2["⌥2"] al["⌥←"] ar["⌥→"] x3["❌"]
  space:3 cmd["⌘"] spc["␣"] sft["⇧"] space ctl["⌃"] ent["⏎"] alt["⌥"] space:3

  classDef plain fill:#e6f6f9,stroke:#0a4f63,stroke-width:2px,color:#04222b
  classDef mod fill:#cdeef4,stroke:#0a4f63,stroke-width:3px,color:#04222b
  classDef seq fill:#aee3ee,stroke:#07414f,stroke-width:3px,color:#03191f
  classDef spec fill:#8ed7e6,stroke:#053541,stroke-width:3px,color:#02141a
  classDef goL0 fill:#adc2d8,stroke:#1d2c3d,stroke-width:3px,color:#060c12
  classDef dead fill:#f2f3f5,stroke:#98a2ae,stroke-width:2px,stroke-dasharray:5 4,color:#5b6673

  class esc plain
  class a0,a1,a2,a4,a5,a6,a7,a8,a9,al,ar,cmd,spc,sft,ctl,ent,alt mod
  class cur,par,sqb,wl,wr,trm seq
  class semi,dqt,sqt,dol,dl2,pct,amp,ast,qm,exc,at,hsh,col,car spec
  class to0 goL0
  class x1,x2,x3 dead
```

### Layer 2 — nav

An inverted-T arrow cluster with the editor folds down the inner column; workspace and window chords on the left.

```mermaid
%%{init: {'theme':'base','themeVariables':{'primaryColor':'#e8f7ec','primaryTextColor':'#06241c','primaryBorderColor':'#0b5946','nodeTextColor':'#06241c','textColor':'#06241c','mainBkg':'#e8f7ec','fontSize':'18px'}}}%%
block
columns 13

  to0["TO0"] g1["⌘1"] g2["⌘2"] g3["⌘3"] x1["❌"] t0b["TO0"] space fa["⊟≡"] hom["⇱"] up["↑"] pgu["⇞"] emj["⇧⌘."] x2["❌"]
  esc["␛"] c1["⌃1"] c2["⌃2"] c3["⌃3"] c4["⌃4"] c5["⌃5"] space fo["⊟"] lft["←"] dn["↓"] rgt["→"] abs["⌥⌫"] x3["❌"]
  cgr["⌃` "] e1["⇧⌘E"] gat["⌘⌥T"] e2["⇧⌘E"] sf4["⇧F4"] f4["F4"] space uf["⊞"] endk["⇲"] del["⌦"] pgd["⇟"] cdl["⌃⌦"] x4["❌"]
  space:3 cmd["⌘"] spc["␣"] sft["⇧"] space ctl["⌃"] sen["⇧⌘⏎"] alt["⌥"] space:3

  classDef plain fill:#e8f7ec,stroke:#0b5946,stroke-width:2px,color:#06241c
  classDef mod fill:#d0efd9,stroke:#0b5946,stroke-width:3px,color:#06241c
  classDef seq fill:#b3e5c2,stroke:#084736,stroke-width:3px,color:#041a14
  classDef goL0 fill:#adc2d8,stroke:#1d2c3d,stroke-width:3px,color:#060c12
  classDef dead fill:#f2f3f5,stroke:#98a2ae,stroke-width:2px,stroke-dasharray:5 4,color:#5b6673

  class esc,f4,hom,up,pgu,lft,dn,rgt,endk,del,pgd plain
  class g1,g2,g3,c1,c2,c3,c4,c5,cgr,e1,e2,sf4,emj,abs,cdl,cmd,spc,sft,ctl,sen,alt mod
  class gat,fa,fo,uf seq
  class to0,t0b goL0
  class x1,x2,x3,x4,tra,trb,trc,trd,tre,trf,trg,trh,tri,trj,trk,trl,trm,trn,tro dead
```

### Layer 3 — numbers

A calculator numpad on the right, 7-8-9 ascending upward, with the operators running down the inner column.

```mermaid
%%{init: {'theme':'base','themeVariables':{'primaryColor':'#fdf3e0','primaryTextColor':'#2e1e00','primaryBorderColor':'#7a5000','nodeTextColor':'#2e1e00','textColor':'#2e1e00','mainBkg':'#fdf3e0','fontSize':'18px'}}}%%
block
columns 13

  to0["TO0"] n1["1"] n2["2"] n3["3"] n4["4"] t0b["TO0"] space mul["*"] s7["7"] s8["8"] s9["9"] d9["9"] x1["❌"]
  t0c["TO0"] c1["⌃1"] c2["⌃2"] c3["⌃3"] c4["⌃4"] x2["❌"] space pls["+"] s4["4"] s5["5"] s6["6"] bsp["⌫"] x3["❌"]
  x4["❌"] x5["❌"] x6["❌"] x7["❌"] x8["❌"] x9["❌"] space mns["-"] s1["1"] s2["2"] s3["3"] x10["❌"] x11["❌"]
  space:3 cmd["⌘"] spc["␣"] sft["⇧"] space kdt["."] zro["0"] alt["⌥"] space:3

  classDef plain fill:#fdf3e0,stroke:#7a5000,stroke-width:2px,color:#2e1e00
  classDef mod fill:#fbe8c4,stroke:#7a5000,stroke-width:3px,color:#2e1e00
  classDef spec fill:#f4c972,stroke:#543700,stroke-width:3px,color:#1c1200
  classDef goL0 fill:#adc2d8,stroke:#1d2c3d,stroke-width:3px,color:#060c12
  classDef alert fill:#ffd5d0,stroke:#8c1008,stroke-width:3px,color:#3d0603
  classDef dead fill:#f2f3f5,stroke:#98a2ae,stroke-width:2px,stroke-dasharray:5 4,color:#5b6673

  class n1,n2,n3,n4,s1,s2,s3,s4,s5,s6,s7,s8,s9,bsp plain
  class c1,c2,c3,c4,cmd,spc,sft,kdt,zro,alt mod
  class mul,pls,mns spec
  class to0,t0b,t0c goL0
  class d9 alert
  class x1,x2,x3,x4,x5,x6,x7,x8,x9,x10,x11 dead
```

### Layer 4 — function

F-keys and the screenshot and window chords, plus every text macro — the email address, the path prefixes, the certificate bypass.

```mermaid
%%{init: {'theme':'base','themeVariables':{'primaryColor':'#f3ecfb','primaryTextColor':'#240c39','primaryBorderColor':'#4a1f72','nodeTextColor':'#240c39','textColor':'#240c39','mainBkg':'#f3ecfb','fontSize':'18px'}}}%%
block
columns 13

  to0["TO0"] vz["␛ZZ"] f2["F2"] gf["⌘F12"] lf12["F12"] t0b["TO0"] space f5["F5"] f10["F10"] f11["F11"] s11["⇧F11"] ap["⌥⌘P"] x1["❌"]
  t0c["TO0"] rr["!!"] rec["⌃⌥⌘S"] c5["⇧⌘5"] fnd["⌘F"] fna["⇧⌘F"] space sgf5["⇧⌘F5"] f4["F4"] mai["📧"] lck["⇧⌘L"] mut["⇧⌥M"] x2["❌"]
  x0["❌"] thk["💭"] c4["⇧⌘4"] cpy["⇧⌘C"] ins["⌃Ins"] pst["⌃⌥⌘V"] space sf5["⇧F5"] ds["./"] uns["🔓"] ds2["./"] ts["~/"] x3["❌"]
  space:3 cmd["⌘"] ses["⇧⌘␛"] cw["⇪w"] space ctl["⌃"] ssp["⇧⌘␣"] alt["⌥"] space:3

  classDef plain fill:#f3ecfb,stroke:#4a1f72,stroke-width:2px,color:#240c39
  classDef mod fill:#e7d9f7,stroke:#4a1f72,stroke-width:3px,color:#240c39
  classDef seq fill:#d8c0f1,stroke:#3d1a5f,stroke-width:3px,color:#1c0a2c
  classDef spec fill:#c7a4e9,stroke:#33154f,stroke-width:3px,color:#160823
  classDef goL0 fill:#adc2d8,stroke:#1d2c3d,stroke-width:3px,color:#060c12
  classDef dead fill:#f2f3f5,stroke:#98a2ae,stroke-width:2px,stroke-dasharray:5 4,color:#5b6673

  class f2,lf12,f5,f10,f11,f4 plain
  class gf,rec,c5,fnd,fna,c4,cpy,ins,pst,s11,sgf5,lck,mut,sf5,cmd,ses,ctl,ssp,alt mod
  class vz,rr,thk,ap,mai,ds,uns,ds2,ts seq
  class cw spec
  class to0,t0b,t0c goL0
  class x0,x1,x2,x3 dead
```

### Layer 5 — F-keys

The Ximi2's F-key block, ported straight across. The left half is transparent bar the three keys ZMK needs and QMK has no equivalent for.

```mermaid
%%{init: {'theme':'base','themeVariables':{'primaryColor':'#fdecf2','primaryTextColor':'#3a0523','primaryBorderColor':'#7a0a4a','nodeTextColor':'#3a0523','textColor':'#3a0523','mainBkg':'#fdecf2','fontSize':'18px'}}}%%
block
columns 13

  to0["TO0"] tra["❌"] trb["❌"] trc["❌"] trd["❌"] t0b["TO0"] space tre["❌"] f7["F7"] f8["F8"] f9["F9"] x1["❌"] x2["❌"]
  trf["❌"] trg["❌"] trh["❌"] tri["❌"] trj["❌"] off["⏻"] space trk["❌"] f4["F4"] f5["F5"] f6["F6"] f12["F12"] x3["❌"]
  trl["❌"] stu["STU"] trm["❌"] trn["❌"] tro["❌"] bld["BLD"] space f10["F10"] f1["F1"] f2["F2"] f3["F3"] f11["F11"] x4["❌"]
  space:3 cmd["⌘"] t0c["TO0"] sft["⇧"] space ctl["⌃"] t0d["TO0"] alt["⌥"] space:3

  classDef plain fill:#fdecf2,stroke:#7a0a4a,stroke-width:2px,color:#3a0523
  classDef mod fill:#fbd8e5,stroke:#7a0a4a,stroke-width:3px,color:#3a0523
  classDef spec fill:#f29ebc,stroke:#560735,stroke-width:3px,color:#250317
  classDef goL0 fill:#adc2d8,stroke:#1d2c3d,stroke-width:3px,color:#060c12
  classDef alert fill:#ffd5d0,stroke:#8c1008,stroke-width:3px,color:#3d0603
  classDef dead fill:#f2f3f5,stroke:#98a2ae,stroke-width:2px,stroke-dasharray:5 4,color:#5b6673

  class f1,f2,f3,f4,f5,f6,f7,f8,f9,f10,f11,f12 plain
  class cmd,sft,ctl,alt mod
  class stu spec
  class to0,t0b,t0c,t0d goL0
  class off,bld alert
  class x1,x2,x3,x4,tra,trb,trc,trd,tre,trf,trg,trh,tri,trj,trk,trl,trm,trn,tro dead
```

---

## Layer notes

Detail that does not fit on a one-line caption, kept out of the maps above so they read as a set.

### Base — the thumbs

**All modifiers live on the thumbs.** There are no home row mods anywhere in this keymap. Five of the six thumb keys are plain; only the right inner one is a tap-dance. The map shows each thumb's tap — the table is what they do when held, or on a double-tap:

| Thumb | Binding | Notes |
|---|---|---|
| Left outer | `&kp RGUI` | Cmd. Right Cmd, faithfully copied from the Ximi2, where every other layer uses left Cmd |
| Left middle | `&lt 2 SPACE` | Space / hold for nav — hence the green |
| Left inner | `&kp LSHFT` | Shift |
| Right inner | `&control_record` | Ctrl; double-tap for the record hotkey. 210 ms, the Ximi2's term |
| Right middle | `&lt 1 ENTER` | Enter / hold for symbols — hence the teal |
| Right outer | `&kp LALT` | Alt |

The lock-screen macro on the right outer column is that column's only surviving tenant on either board. Its final home is still an open question in the plan.

### Symbols — what sits where

Left hand: the three bracket-pair macros stacked vertically, each leaving the cursor inside, then the quotes, the colons and the shifted number row. Right hand: **⌥+digit** for all ten digits, which is an application switcher, plus word and paragraph motion and a terminal launcher.

### Nav — what sits where

Left hand: Cmd+1..3, Ctrl+1..5, the screen-capture and terminal chords, F4 and Shift+F4. Right hand: an inverted-T arrow cluster with Home, End, PgUp, PgDn and Delete, the three editor fold macros down the inner column, and word-wise delete on the pinky.

### Numbers — the emptiest layer

The operators `* + -` run down the inner column, which is where they belong and where the Corne did not have them before. The left hand carries digits 1–4 and Ctrl+1..4, and the whole bottom-left row is `&none`.

The second `9` on the right pinky is a known Ximi2 defect, drawn in red. It is reproduced here on purpose: the two boards being identical is worth more than one key being right on one of them.

### Function — sticky, never locked

`&caps_word` sits on the left inner thumb. The left outer column is empty: Caps Lock used to live there and moved to the Ximi2's left thumb, a tap-then-hold the Corne cannot mirror, so the Corne lost the key rather than gained a gesture. Layer 4 has a sticky form and no locked form, by design.

`./` appears twice because `M4` and `M12` are both `./` in the Vial table. Another deliberate reproduction.

### F-keys — what the left half is for

Bluetooth used to fill that left half. It has moved out entirely into the `o`+`p` combo grid, which means the radio occupies no keymap position on either board and this layer is down to the three things ZMK needs and QMK has no equivalent for: `STU` is ZMK Studio unlock, `⏻` is soft off, `BLD` the bootloader. They sit on `z`, `g` and `b`, none of which the layer-entry combo `x`+`c`+`v` touches.

That takes layer 5 from fifteen divergent positions to three, and it is the single largest parity gain since the port itself.

Reaching the layer is `x`+`c`+`v` held, or `z`+`x`+`c`+`v` to lock it, the same on both boards. Nothing on the base thumbs opens it any more.

---

## Corne macros

Twenty-five definitions. Twenty are numbered to match the Vial macro table in `akiva.vil` rather than renamed, with the gaps being the Vial slots that are empty or bound only to Ximi2-only keys. The other five are Bluetooth, which the Ximi2 has no equivalent for.

| Macro | What it does |
|---|---|
| `m0` `m1` `m2` | `{}`, `()`, `[]`, cursor left into the pair |
| `m3` | terminal: `⌃⌥⌘T`, wait 100 ms, `⌥3` |
| `m4` `m12` | `./` — both, as in the Vial table |
| `m5` `m6` `m7` | editor fold, unfold, fold all |
| `m8` | `⌥⌘P` |
| `m9` | Escape then `ZZ` — vim exit |
| `m10` | `⌃⌘Q` — lock screen |
| `m13` `m14` | `⌥⌃←` and `⌥⌃→` |
| `m15` | the email address |
| `m20` | `⇧⌥T`, wait 300 ms, `!!`, Enter — re-run the last shell command |
| `m22` | `thisisunsafe` |
| `m23` | `think hard and be smart` |
| `m24` | `~/` |
| `m27` | `⌘⌥T` |
| `btclr0`-`btclr4` | select Bluetooth profile 0-4, wait 30 ms, then clear it |

The twelve legacy named macros are gone, the nine byte-identical duplicates with them, and with them the pair whose `fold` and `expand` names were inverted.

The five `btclr` macros exist because ZMK's `BT_CLR` takes no profile index — it clears whichever profile is current. Clearing a *named* profile therefore means selecting it first, which is a sequence, which is a macro.

---

## Corne behaviors

One tap-dance. No hold-taps, no mod-morphs, no `&mt` anywhere.

- **`control_record`** — tap for Ctrl, double-tap for `⌃⌥⌘\`. 210 ms, matching the Ximi2. Kept on purpose: the Corne has no spare key for the record hotkey, so the double-Ctrl habit is what transfers between boards.

The `gui5` and `alt5` triple-tap dances are gone. They put a 400 ms tapping term on Cmd and Alt, which are the two modifiers this user's zellij config leans on hardest. `td10` is gone too; its lock-screen tap is now a plain macro on the key it already shared.

The left thumb is a plain Shift. The Ximi2's dance there now carries both caps behaviours — Caps Word on a double-tap, Caps Lock on a tap-then-hold — and ZMK can express neither slot faithfully: it has no tap-hold at all, and a double-tap-to-caps would fire while typing two capitals in a row. `&caps_word` is on layer 4's left inner thumb on both boards. Caps Lock is the one casualty: it vacated layer 4's left outer column when the Ximi2 gave it a thumb gesture, and the Corne has no binding for it anywhere. The `capslock` macro stays defined in the keymap, unbound, for whenever a home turns up.

`&lt` now carries `quick-tap-ms = 200`, `require-prior-idle-ms = 125` and `flavor = "tap-preferred"`, so a fast Space or Enter cannot resolve as a layer hold. This is a Corne-only improvement; QMK has no equivalent knob, so the Ximi2 goes without.

---

## Corne combos

Forty combos, 150 ms timeout, **all scoped to `layers = <0>`** — they fire on base and nowhere else. The Ximi2 leaves its combos global; the Corne leads here.

Every one of them is drawn key by key in [`combos.md`](combos.md), which uses its own two-colour scheme — amber for a combo's keys, cyan for the shared Bluetooth anchor — rather than the per-layer hues above. What follows is the summary.

**Punctuation** — the mnemonic core of the layout, and the part that works best:

| Combo | Output | Logic |
|---|---|---|
| `d`+`r` | `/` | traces the glyph, lower-left to upper-right |
| `e`+`f` | `\` | traces the glyph, upper-left to lower-right |
| `s`+`f` | `-` | |
| `x`+`v` | `_` | directly below dash, same two columns |
| `t`+`g` | `;` | vertical, outer column |
| `g`+`b` | pipe | vertical, outer column |
| `j`+`l` | `+` | |
| `m`+`.` | `=` | directly below plus, same two columns |
| `q`+`s` | `[` | |
| `a`+`w` | `]` | |
| `q`+`e` | Escape | |

Almost none of these sit on two adjacent columns of the same hand — they skip a column or run vertically, which is what keeps a typing roll from firing them. `d`+`r`, `f`+`e`, `q`+`s` and `a`+`w` are the grandfathered exceptions, not a precedent.

**Layer access** — a uniform momentary/locked pair per layer, identical on both boards:

| Combo | Goes to |
|---|---|
| `s`+`e`+`f` | nav, momentary |
| `a`+`s`+`e`+`f` | nav, locked |
| `s`+`d`+`f` | numbers, momentary |
| `a`+`s`+`d`+`f` | numbers, locked |
| `x`+`c`+`v` | layer 5, momentary |
| `z`+`x`+`c`+`v` | layer 5, locked |
| `j`+`k`+`l` | function, sticky |
| `m`+`,`+`.` | function, sticky |

Function has a sticky form and no locked form on purpose — one-shot is the point, and a lock would be a trap.

**Pointer** — four buttons, no movement and no scroll; the trackpad owns the pointer, and these four survive because no trackpad maps them:

| Combo | Button |
|---|---|
| `i`+`p` | button 5 — browser forward |
| `k`+`⌫` | button 4 — browser back |
| `q`+`d` | left click |
| `a`+`c` | right click |

**Bluetooth** — sixteen combos on one shared anchor, and the only part of the keymap with no Ximi2 equivalent, since the work board is wired.

`o`+`p` is the anchor. Every Bluetooth combo holds it, and a third key names the profile. The row that third key sits on picks the verb:

| Row | Verb | Profile 0 | 1 | 2 | 3 | 4 |
|---|---|---|---|---|---|---|
| top | select | `q` | `w` | `e` | `r` | `t` |
| home | disconnect | `a` | `s` | `d` | `f` | `g` |
| bottom | clear | `z` | `x` | `c` | `v` | `b` |

Plus one escalation: `o`+`p`+`z`+`x`+`c`+`v` clears every profile. Six keys, the largest combo in the keymap.

The whole radio is one posture with fifteen destinations. **ZMK counts profiles from 0**, so the first key of a row is profile 0, not profile 1.

The anchor has a cost, and it is paid explicitly. `o` and `p` are adjacent columns of the same hand — the exact pattern every other combo here avoids — and `t`-`o`-`p` is a triple that "top" and "stop" roll straight through. So every Bluetooth combo carries `require-prior-idle-ms = <250>` and will not fire unless the keyboard was already idle, which mid-word it never is. A deliberate press starts from a standing stop and is unaffected.

Every key in the grid is inside the 36-key core, so all sixteen survive the move to a smaller board. Bluetooth exists nowhere else in the keymap — there are no radio keys on any layer, which is what lets layer 5 hand its left half back to `&trans` and match the Ximi2.

**Everything else** — `q`+`b` types the prose macro.

Many of these combos overlap as subsets of each other — `s`+`f` inside the four home-row layer combos, `x`+`c`+`v` inside `z`+`x`+`c`+`v`, the four bottom-row Bluetooth clears inside clear-everything. That is accepted, not a defect: both QMK and ZMK defer to the longer combo, so the cost is a slow-roll timing tax, not an always-on collision. In particular, do not "fix" `s`+`f` out of the layer-access family.

---

## Configuration

Active in `corne.conf`: ZMK Studio, pointer buttons (`CONFIG_ZMK_POINTING`, for the four pointer combos), raised combo budgets, soft-off, experimental BLE features, sleep with a 15-minute idle timeout, split battery reporting, +8 dBm TX power, and an aggressive 1 ms press debounce (the default is 5). RGB underglow and display are commented out.

Both combo budgets are raised for the Bluetooth grid: `p` now sits in seventeen combos, past the default of five per key, and clear-everything needs six keys, past the default of four per combo.

The deprecated mouse-emulation flag is gone along with mouse movement and scroll.

---

## Known issues in the current layout

**Carried over from the Ximi2 on purpose.** These are real defects in `akiva.vil`, reproduced here because two identical boards are worth more than one correct key:

- Layer 3's duplicate `9` on the right pinky.
- Layer 2 binds `⇧⌘E` on two different keys.
- `M4` and `M12` are both `./`.

**Still open on the Corne**

- Layer 5 holds `&bootloader` and `&soft_off`, and the layer has two entrances, one of them a three-key combo. Nothing guards them once you are there; they sit on keys the entry combo does not touch, which is mitigation, not a fix.
- Bluetooth is now reachable only through combos. If a combo ever stops firing — a debounce change, a timeout change, a dead switch under `o` or `p` — there is no keymap position to fall back on, and re-pairing needs the physical reset button.
- The Bluetooth anchor `o`+`p` sits on two adjacent columns of the same hand, breaking the rule the rest of the combo set follows. `require-prior-idle-ms = <250>` is what makes it safe, so that number is load-bearing: lower it and `t`-`o`-`p` starts switching profiles inside the word "stop".
- The 1 ms press debounce widens the window for a slow roll to emit `-` instead of entering a layer. The combo overlap itself is accepted; the debounce is not examined.
- The lock-screen macro is the only tenant of the right outer column, on either board. It needs a home inside the 36-key core before that column can be retired.
- Tab and Escape still sit on the left outer column and have no new home.

---

## The second keyboard — Ximi2

`akiva.vil` is a Vial export for a **Ximi2** used at work: QMK rather than ZMK, already fitted with a trackpad, and carrying a four-key extra cluster per half that has no Corne equivalent (window-management chords, a record hotkey, browser refresh, hyper shortcuts). It is the board the migration is running on, and it is now several stages ahead of the Corne.

What changed: the right outer column is dead on every layer except one tenant; mouse movement and scrolling are gone; backtick moved down onto the old `/` key; layer access was rebuilt into a uniform momentary/locked pair; and two structural Vial bugs were found and fixed.

### Ximi2 layer maps

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
·    M23 ⇧⌘4 ⇧⌘C ⌃Ins ⌃⌥⌘V     ⇧F5  M4  M22 M12   M24   ·
```

**Layer 5 — F-keys**
```
TO0  ·  ·  ·  ·  TO0        ·  F7 F8 F9  ·    ·
 ·   ·  ·  ·  ·   ·         ·  F4 F5 F6  F12  ·
 ·   ·  ·  ·  ·   ·        F10 F1 F2 F3  F11  ·
```

The left half is `KC_TRNS`, not `KC_NO` — it was reserved for the Bluetooth controls a wired work board does not need. The Corne has since put Bluetooth entirely into combos, so nothing needs that room any more and both boards now leave it transparent bar the Corne's three ZMK-only keys. Both middle thumbs are `TO(0)`.

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

`q`+`b` fires the prose macro and `q`+`e` gives a second Escape. Both are re-anchored inside the 36-key core, so neither dies with the outer columns — `⇥`+`b`, which an earlier draft of this section recorded, no longer exists on either board.

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

**Layer 5's left half stays `KC_TRNS`.** Originally reserved for the Bluetooth controls the Corne needs and the work board does not; the Corne then moved Bluetooth into combos, so the reservation turned out to be unnecessary and the transparency is now simply parity. The consequence is real and accepted either way: while locked into layer 5, every left-hand combo is still armed.

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
- ~~The Corne keymap bindings are unported.~~ Ported. The Corne now matches this file position for position, so every remaining item on this list is a defect on **both** boards.
- Combos are still global on the Ximi2; the Corne has scoped its own to the base layer, and QMK should follow.
- The base-layer left thumb is `KC_RGUI` while every other layer uses `KC_LGUI`. Copied faithfully to the Corne rather than silently corrected; worth deciding one way or the other.

---

## Planned transition

Full plan: [`32-keys.md`](32-keys.md).

The goal is a **beekeeb Toucan2, 36-key build** — a Cantor/Piantor layout with a multi-touch trackpad, which is this Corne minus the two outer pinky columns. All six thumb keys survive.

The approach is deliberately gradual, and the hardware is bought **last**, only after 36 keys already feels normal on the boards already owned.

| # | Stage | Corne | Ximi2 |
|---|---|---|---|
| 1 | **Make both maps honest** — fix diagrams, delete dead and duplicated macros. No key changes | done — diagrams regenerated, twelve legacy macros deleted, macro numbering matched to Vial | partial — mouse cruft gone, but orphan macros, the duplicate `9` and the double-bound `SGUI(E)` remain |
| 2 | **Let the trackpads take the pointer** — delete movement, scroll and clicks; keep browser back/forward | done — movement and scroll deleted, four pointer combos kept | done — movement and scroll deleted, four pointer combos kept by choice |
| 3 | **Take your hands off the brake** — plain modifiers, sane sticky timeout, per-layer combo scoping, layer-tap protection | done — `gui5`/`alt5` and `td10` deleted, the 60-second sticky window dropped, combos scoped to base, `&lt` given quick-tap, prior-idle and tap-preferred | partly declined — `td[4]` kept for Corne parity, `td[2]` still gating layer-1 Shift, combos still unscoped |
| 4 | **Converge the two keyboards** — identical 30-key core on both | done — every binding on all six layers ported from `akiva.vil`, and layer 5 down from fifteen divergent positions to three now that Bluetooth is combos-only | done |
| 5 | **Two homes for every orphan** — new locations go live while the old keys still work | done — `/`, backtick and the square brackets moved with the port | done for `/`, backtick and the square brackets |
| 6 | **Retire the right column** — nearly free after stage 2 | done except `M10` (lock screen), matching the Ximi2 | done except `M10` (lock screen), which is deferred |
| 7 | **Retire the left column** — Tab, Escape and backtick; the real test, and the only gate on buying hardware | started — the outer bottom key is dead and the `TO(0)` escape hatch is pre-placed on the `t` column | started — the outer bottom key is dead and the `TO(0)` escape hatch is pre-placed on the `t` column |
| 8 | **Order the Toucan2** | blocked on stage 7, both boards | |

Home row mods are an optional side quest that can run at any point after stage 3 and deliberately gates nothing.

Two rules run through the whole plan: never advance a stage on a schedule, only when the previous one is genuinely settled; and never advance on one keyboard alone, because divergence between the two is the likeliest way the migration stalls. The second rule was suspended while the work board ran ahead; stage 4 has now repaid that, and both rules are back in force for stage 7.

---

## ZMK reference

- Keymaps — <https://zmk.dev/docs/keymaps>
- Behaviors — <https://zmk.dev/docs/keymaps/behaviors>
- Combos — <https://zmk.dev/docs/keymaps/combos>
- Macros — <https://zmk.dev/docs/keymaps/behaviors/macros>
- Pointer and mouse keys — <https://zmk.dev/docs/keymaps/behaviors/mouse-emulation>
- Bluetooth — <https://zmk.dev/docs/keymaps/behaviors/bluetooth>
