# Blacklight — design brief

The current-state snapshot (playbook section 1). Read it before `CLAUDE.md`, whose log is history and may
describe a product that no longer exists; this file is the recall point.

**A brief is a concept, not a source of truth.** Usability, and what actually sounds best, outrank it every
time. When a decision contradicts this file, update the file; never argue a finding down by citing an earlier
intention. A settled row is replaced by the decision and its date, never overwritten silently. Every open row
carries a **lean**: an inclination for discussion to push against, never a decision.

> Written 2026-10-25 in a cloud design session, from `udidsp-hq/skills/new-product/SKILL.md` stage 3 and
> Clearlight's code as it stands at `38a16fd` (Clearlight 1.0.0, core pin v0.25.0). **No code exists yet.** Before
> the owner greenlights it, check this brief against every `udidsp-decisions` entry dated after 2026-10-25.

- **Shipping name and technical identity:** Blacklight (the owner, 2026-10-25). · **Moonbase id:** `blacklight`
  (created in Moonbase by the owner, 2026-10-25) · **plugin code:** `Blkl` (proposed, open row 3) · **bundle:**
  `com.udidsp.blacklight` · **data folder:** `Blacklight` (proposed) · **preset extension:** `.blpreset` (proposed)
- **Kind:** a **Clearlight edition** (a Variant, `new-product` section 4 Q9), built from Clearlight's repo and trunk
  with a build-time flag, as Ghostlight is built from Ghostlight Pro (the owner, 2026-10-25; decision 33's model).
- **Price tier:** low, about $19 to $29 (the owner, 2026-10-25); the exact price is set in Moonbase. It would be the
  line's cheapest (Ghostlight and Toolbox are $39).
- **Core pin:** Clearlight's (v0.25.0 today). An edition shares its product's pin (decision 8).
- **Status:** beta until the owner releases it (decision 6).

## 1. Mission

**A reverb you put on a track, set with two knobs, and stop thinking about.** Blacklight is Clearlight's engine in a
closed box: SIZE says how big the space is, DECAY says how long it rings, and everything else (pre-delay, early
reflections, diffusion, damping, the solid low end, Clarity's carve) is decided for you, moving with those two knobs
the way a real room's properties move together. It keeps Clearlight's character, huge and clean while the source
stays clear, at a price that makes it an easy first purchase in the line.

**The customer, and how existing plugins let them down:**
- Producers and songwriters who want a good reverb now and don't want to learn ten controls: a full reverb's
  controls slow them down, and presets bury them in choices.
- People building a first plugin collection: Blacklight is the line's door, at an impulse-buy price.
- Clearlight's own customers on a busy session: a cheap, light instance for the tracks that only need "some space".

**What it is not** (so the next session does not add it):
- **Not a second Clearlight.** No third character knob, no ADVANCED page, no tempo sync, no dynamics or
  colour stages (Warm, Compression, Ducking, Dynamic EQ). The owner, 2026-10-25: *"if we had too many controls we're
  just recreating clear light."* Every extra control is a step toward that.
- Not a "lite" Clearlight with controls greyed out. It is its own product with its own voicing: the two knobs move
  several of Clearlight's settings at once (section 3), which no Clearlight preset does.
- Not a plate, spring or algorithm-choice box: one space, scaled.

## 2. The editorial line

**SIZE and DECAY are the only questions the user is asked.** Every other setting is answered by the box, and its
answer is a function of SIZE and DECAY or a constant, never a hidden control.

The rules every mapping obeys:
1. **A real room's properties move together.** A bigger space starts later, spreads its early reflections wider,
   is more diffuse and has a wider, more solid low end; a longer ring gets darker, as air and walls absorb highs
   over time. The mapping is a physical story first and a curve second.
2. **Inside Clearlight's settled-by-ear bounds, always**: Size and Diffusion at or above their 0.30 floors, Decay at
   or above 0.35 s, Shape and Early inside their 0.55 ceilings, Low-End Focus at its 300 Hz crossover. A mapping
   never reaches a region Clearlight's listening already ruled out.
3. **Every reachable SIZE x DECAY corner is usable** (decision 20, rule 3). A tiny room with a 30 s ring is odd, and
   it must still sound clean and intended, not broken.
4. **Smooth everywhere.** No mapping has a step or a corner a slow knob turn could hear; a jump (preset load,
   automation step) is crossfaded where Clearlight crossfades it (Size jumps, decision 22).
5. **No claim the DSP does not make.** The panel and guide describe SIZE and DECAY as what you hear, and never
   promise "auto-adapting" or "intelligent" behaviour. Clarity reacts to the source and the guide says that plainly.

**What the line excludes** (each could come back only by the owner's decision, never by drift):
- any third sound control; tempo sync; the dynamics and colour stages; an EQ page; visible pre-delay.
- (A Freeze was on this list until the owner's ruling of 2026-10-25: *"We need freeze button like the other reverbs
  (on the top bar)"*. It is in, as a header button like Clearlight's and Ghostlight's; see section 10, Settled.)

## 3. The signal chain

Blacklight is Clearlight's `renderStereo` with its parameters supplied by the edition instead of by the host. Each
stage is **HAVE** (exists in Clearlight or core, used as is), **ADAPT** (exists, needs changing) or **NEW**.

```
in -> IN -> dry copy
          -> Clarity analysis (fixed 0.5) -> tank spectral multipliers
          -> Contour stage (Shape fixed, Spread(SIZE), Early(SIZE)) -> ER Gap (0) -> tank input
          -> FDNReverb (Size(SIZE), Decay(DECAY), Pre-Delay(SIZE), Diffusion(SIZE), Damp(DECAY), Low Cut fixed)
          -> early/late blend -> Clarity carve -> StereoField (Width 1, Focus(SIZE)) -> Clipper (always last)
          -> MIX -> OUT -> out
```

| Stage | HAVE / ADAPT / NEW | Where it lives | In Blacklight |
|---|---|---|---|
| Input trim, NaN door, mono handling, bypass crossfade, rest on silence | HAVE | Clearlight | unchanged |
| Clarity (analysis + carve) | HAVE | Clearlight `dsp/ClarityEngine` | fixed at 0.5, Clearlight's default (the owner, 2026-10-25) |
| Contour stage, ER Gap, ER Tone, ER Width | HAVE | Clearlight `dsp/` | Spread and Early from SIZE; Shape, Gap, Tone, Width fixed at Clearlight's defaults |
| FDNReverb | HAVE | core | Size, Decay, Pre-Delay, Diffusion, Damp from the macros; Low Cut 150 Hz; Knee 0; Freeze off |
| StereoField | HAVE | Clearlight `dsp/StereoField` | Width 1.0; Focus from SIZE |
| Dynamic EQ, Warm, Compression, Ducking | HAVE | core | at their inert values (bit-transparent), not registered as parameters |
| Clipper | HAVE | core | unconditional, amount 0 (the safety half only) |
| Listen to Early, Freeze, tempo sync | HAVE | Clearlight | absent: off, not registered |
| Kill the tail | HAVE | core `TailKill` | kept (open row 7) |
| **The macro map** | **NEW** | Clearlight `Source/BlacklightMacros.h`, JUCE-free | SIZE and DECAY to the engine's settings |
| **The edition flag** | **NEW** | Clearlight CMake + `PluginProcessor` | `CLEARLIGHT_EDITION_BLACKLIGHT=1` |
| **The compact editor** | **NEW** | Clearlight `Source/blacklight/` | section 5 |

### 3.1 The macro map (proposed, for the owner's ears in Phase 0)

The knobs' face values: SIZE `s` is 0..1 (shown 0-100%), DECAY `d` is seconds. All figures below are what the
mapping hands Clearlight's **face values** (the same units Clearlight's presets use), so Clearlight's own floors,
ceilings and skews still apply on top.

Derived from Clearlight's 45 factory presets, which already move these settings together by hand (small rooms:
Size 0.30-0.45, pre-delay 5-18 ms, Diffusion 0.3-0.6, Early 0.4-0.5; halls: Size 0.65-0.85, pre-delay 22-40 ms,
Diffusion 0.8-0.95, Early 0.2-0.35, Focus rising with size; long and dark presets Damp 2.2-6 kHz). **None of those
presets has been auditioned**, so they are a starting shape, not a voicing.

**From SIZE (`s`):**

| Engine setting | Formula | s = 0 | 0.35 | 0.55 (default) | 0.85 | 1.0 |
|---|---|---|---|---|---|---|
| Size (face) | `s` (Clearlight maps it to 0.30..1) | 0 | 0.35 | 0.55 | 0.85 | 1.0 |
| Pre-Delay | `4 + 40 s²` ms | 4.0 | 8.9 | 16.1 | 32.9 | 44.0 |
| Diffusion (face) | `0.55 + 0.35 s` | 0.55 | 0.67 | 0.74 | 0.85 | 0.90 |
| Early (face) | `0.50 - 0.20 s` | 0.50 | 0.43 | 0.39 | 0.33 | 0.30 |
| ER Spread | `0.55 + 0.25 s` | 0.55 | 0.64 | 0.69 | 0.76 | 0.80 |
| Low-End Focus | `0.20 + 0.30 s` | 0.20 | 0.31 | 0.37 | 0.46 | 0.50 |

**From DECAY (`d`, seconds):**

| Engine setting | Formula | 0.35 s | 1 s | 2.5 s | 4.5 s (default) | 12 s | 30 s |
|---|---|---|---|---|---|---|---|
| Decay | `d` | 0.35 | 1 | 2.5 | 4.5 | 12 | 30 |
| Damp | `9000 · d^-0.24`, held to 4000..9000 Hz | 9000 | 9000 | 7223 | 6273 | 4957 | 4000 |

**Fixed:** Shape 0.275 (Clearlight's default, front-loaded), ER Gap 0, ER Tone 0, ER Width 1.0, Low Cut 150 Hz,
Clarity 0.5, Width 1.0, Knee 0, every dynamics and colour stage 0, Clipper 0, Freeze off, Listen to Early off,
pre-delay sync Free.

**Why these shapes:**
- Pre-delay is quadratic so small rooms stay tight (under 10 ms below 35%) and the gap only opens for halls.
- Early falls as the room grows: the far walls are further, so the direct-to-early balance moves toward the tail,
  matching Clearlight's halls (0.2-0.35) and rooms (0.4-0.5). It never comes near the 0.55 stage ceiling (face 0.5
  is stage 0.275).
- Spread grows with size: the early field lasts longer in a bigger room. Spread is Clearlight's costliest control;
  the top (0.80) is inside what its presets already use.
- Focus grows with size: big spaces get a more solid, centred low end, and it stays under the 0.5 where users start
  hearing the bass narrow.
- Damp follows DECAY, not SIZE: what makes a long tail tiring is its highs ringing on. The curve passes 6.3 kHz at
  the default 4.5 s, close to Clearlight's 7 kHz default.

**Decay's range: 0.35 s to 30 s** (the owner, 2026-10-25), skew 0.35, which puts 4.5 s almost exactly at the knob's
middle (face 0.502). Clearlight goes to 60 s as its own point of difference; endless washes stay Clearlight's.

**Both macros are continuous and monotonic**, so a turned knob glides through Clearlight's own smoothing (20 ms
ramps, Pre-Delay's and Size's crossfades). The map is evaluated once per block from the smoothed knob values; it is
JUCE-free so a test can sweep it on its own.

## 4. Controls and the parameter table

A new instance opens at the defaults (decision 20, rule 1). **The ids FREEZE before the factory bank is authored.**
The ids are Clearlight's own, so Clearlight's tests, the MIX lock and BankCheck read them unchanged.

| id | Name | Type | Range | Default | Unit | Automatable | In presets |
|---|---|---|---|---|---|---|---|
| `size` | SIZE | float | 0 … 1 | 0.55 | % | yes | yes |
| `decay` | DECAY | float | 0.35 … 30, skew 0.35 | 4.5 | s | yes | yes |
| `mix` | MIX | float | 0 … 1 | 0.30 (open row 4) | % | yes | yes (held by the MIX lock) |
| `inputGain` / `outputGain` | IN / OUT | float | -24 … +24 | 0 | dB | yes | yes (held by the MIX lock) |
| `freeze` | FREEZE (header) | bool | | off | | yes | **no** (as Clearlight: a preset neither sets nor clears it) |
| `killTail` | Stop the tail | bool, momentary | | off | | yes | **no** |
| `panelBypass` | POWER | bool | | off | | yes | **no** |
| `bypass` | host bypass | bool | | off | | yes | **no** |

Nine parameters since the owner's Freeze ruling of 2026-10-25 (eight as first built; the code follows on his go).
Every other Clearlight parameter is **not registered** in this edition (Ghostlight's first
treatment), so a host never shows a control the panel doesn't have. The processor's parameter pointers for them are
guarded out and the edition supplies values in their place.

## 5. The panel

**A small black box with one quiet display** (the owner, 2026-10-25, choice 2). It keeps the family's header and
OUTPUT card; it is smaller than the family's 980 px stack, which is a recorded exception to `udidsp-panel`
(open row 1). `docs/panel-mockup.md` has the ASCII mockups.

- **Base size: 720 x 380** (proposed). Margins 16, header 44, gap 12, body 292, margin 16. No footer (decision 3: no
  pages, no footer row).
- **Header** (decision 1, `headerLayout::place`): BLACKLIGHT and its subtitle at the left; no mode buttons; from the
  right POWER, the menu, the preset field with < >; the licence badge centred in what is left. **No visualizer
  button**: the display is the box's face and does not collapse, so the header closes that gap.
- **Body, left (504 wide):** the display on top (504 x 130), then one card, **SPACE**, holding SIZE and DECAY as two
  large knobs (504 x 150). DECAY carries the stop-the-tail corner button, as in Clearlight.
- **Body, right (172 wide):** the family's OUTPUT card, full body height: MIX large, IN and OUT beneath
  (`BottomRow::inOut`), the two meters, the MIX lock padlock (decision 11: MIX, IN and OUT).
- **Accents:** SPACE in Clearlight's space accent; OUTPUT `theme::good`. The panel ground is the family's, the
  display's well is darker still: the "black box".
- **Base on core's `PanelShell`** (core v0.25.0, Fluxwave's adoption): header, About, menu, prompts, licence, panel
  size. Clearlight's own editor has not adopted it; Blacklight's can from its first line (open row 9).
- **Panel size** (decision 5): core's `panelSize`, 75/100/125/150%; width stored, height derived.

### 5.1 The display: the light in the box

One layer, drawn in the engine's own terms (`udidsp-panel` section 5), fed from the 30 Hz timer through atomics the
processor publishes while a panel is open (decision 20):

- **A soft horizontal glow** in the middle of a black well. Its **width is SIZE** (the room's extent, from the knob's
  smoothed value), its **brightness is the wet output's real level** (a published RMS of the finished wet before
  MIX, in dB, mapped from -60 dBFS dark to -6 dBFS full), and its **colour cools toward Clearlight's accent as Damp
  falls**, so a long, dark tail glows deeper.
- **It decays with the sound, not with the knob.** When the source stops, the glow fades at the tail's real rate, so
  a 30 s DECAY visibly hangs and a short room snaps dark. That is the whole point of the display.
- **Honest at rest and in bypass:** no signal, no light; bypassed, the well is dark and dimmed by the family's
  bypass veil. The display never animates on its own.
- **Optional detail** (open row 10): the early reflections as faint specks arriving before the glow builds, from
  the contour stage's published tap times, so SIZE's pre-delay and spread can be seen.

## 6. Platform facts (decided up front)

| | Decision |
|---|---|
| Bus layouts | Clearlight's: stereo, mono, mono in / stereo out |
| Sidechain / MIDI | none |
| Reported latency | 0 (Clearlight's: every stage is causal) |
| Reported tail | Clearlight's `getTailLengthSeconds` unchanged: it is worked out from the settings, and with the edition's fixed values (dynamics 0, Clarity 0.5, Knee 0, no Freeze) it is never infinite. About 18 s at the defaults (Clearlight's figure, same Decay); at DECAY 30 s it roughly doubles that |
| Behaviour in silence | Clearlight's rest: exact zeros, near 0.02% of a core |
| Standalone | none: an effect ships without one (decision 30) |
| AU type / AAX category | `aufx`; AAX category Reverb (the owner's pick: confirm) |
| Data folder | `Blacklight` (own folder: it is sold as its own product, as Ghostlight's is `Ghostlight SE`) |
| Presets | `.blpreset`, root tag `BlacklightPreset`, bank `productId` `blacklight`; Clearlight presets and banks refused, both ways (Ghostlight's rule) |
| Installer | its own Inno Setup `AppId`, `{1B5000E2-C221-4FCD-9C32-9C5BE2DA4712}` (generated 2026-10-25, never to change); its own signed `.pkg` on the Mac mini |

## 7. Budgets (before any code, decision 20)

- **CPU:** the default under 10% of one core, every reachable setting under 25% (48 kHz stereo, 512-sample blocks).
  Expected near Clearlight's defaults, **3.5%**, since the costly stages stay (Clarity's FFT, the contour stage's
  taps) and the dynamics stages are inert; the worst Blacklight setting (SIZE 100%, Spread 0.80, DECAY 30 s) should
  sit under Clearlight's worst case, 4.7%. Measured, not assumed, by `CpuBenchmark` in the edition (new cases: the
  four SIZE x DECAY corners).
- **The macro map's cost:** one `pow` (Damp) and a few multiply-adds per block, nothing per sample.
- **Latency:** 0. **Aliasing:** no nonlinear stage is in use (the Clipper's safety half only).

## 8. Phases and their gates

A phase does not start until the previous gate passes. Every listening gate is the owner's.

| Phase | Work | Measured by | Gate |
|---|---|---|---|
| 0 | **Voice the macros.** A render tool in Clearlight's repo (`Tests/BlacklightVoicing.cpp`, `EXCLUDE_FROM_ALL`) that runs Clearlight's real processor with the map applied: a 4 x 4 SIZE x DECAY grid on three clips, plus the A/B pairs in `docs/listening-sheet.md`. No edition code yet. | the map's own test (monotonic, inside every floor and ceiling) | the owner's ears on the listening sheet |
| 1 | **The edition.** CMake target `Blacklight`, the flag, eight parameters, the map wired into `renderStereo`, the edition's preset identity, Moonbase `blacklight` | the edition-equivalence check (section 9), every Clearlight suite for both editions | all green, `--break` caught |
| 2 | **The compact panel**, a scaffold first (knobs and OUTPUT, the display as an empty well), then the real one | PanelRender for the edition | renders looked at; header, badge, size and MIX-lock checks pass and break |
| 3 | **The display** | a display test handle: dark at rest, bright under a tone, fading with the tail at DECAY's rate | the owner's look |
| 4 | **Ship:** the factory bank (`docs/factory-presets.md`), the guide, facts, pictures, the eight examples, the product page, Windows and Mac installers, AAX | the release checklist (`udidsp-core/docs/RELEASE-CHECKLIST.md`) | the owner releases it |

## 9. Checks: what "measured" means here

Every check has a broken bar and is caught in `--break`.

| Check | Bar | Broken bar |
|---|---|---|
| **Edition equivalence** (the key one) | Blacklight at a SIZE x DECAY grid renders bit-identical to Clearlight's processor given the mapped values and the fixed settings | the map handed a different Diffusion |
| Macro map | monotonic in each knob; every output inside Clearlight's floors and ceilings across a 101 x 101 sweep | Early pushed past face 1.0 |
| Parameter list | exactly the eight ids of section 4, `bypass` the only one a host may not automate | a ninth (any Clearlight id) registered |
| The ChainHash, per edition | Blacklight's own baseline file | (Clearlight's existing break) |
| Tail | the reported tail covers the measured 140 dB ring at the four corners | the old `decay + 0.5 s` |
| Presets refused across editions | a `.clpreset` and a Clearlight bank never load in Blacklight, and the reverse | the root-tag check skipped |
| MIX lock | holds exactly MIX, IN and OUT over every factory preset, both load paths | the family's old pair |
| Panel | header order, badge words and size, no footer, size memory, nothing overlapping at 720 x 380 | Clearlight's existing PanelRender breaks |
| Display | dark on silence and in bypass; brightness tracks the wet level; fades with the tail | brightness from the SIZE knob instead of the level |
| CPU | under the budget at defaults and at the four corners; output hashes held | (CpuBenchmark's own) |

## 10. Open decisions

Asked one at a time, numbered, each explained as what you hear (the owner's standing rules).

| # | Question | Blocks | Options | Lean |
|---|---|---|---|---|
| 1 | ~~The compact panel~~ **Settled 2026-10-25: 800 x 490, the approved mock** (below; `docs/panel-mockup.md`) | | | |
| 2 | ~~DECAY's range~~ **Settled 2026-10-25: 0.35-30 s** (below) | | | |
| 3 | The plugin code | Phase 1 | `Blkl`; another free code (one upper-case letter, first) | `Blkl`: free against the line's list. **Built at the lean on 2026-10-25, provisional** (the owner has not ruled; it freezes at the first public build) |
| 4 | MIX's default | Phase 1 | 30%; Clearlight's 35%; 100% (send use) | 30%: an insert default a little drier than Clearlight's. **Built at the lean on 2026-10-25, provisional** |
| 5 | The macro map's shapes and numbers | Phase 1 | as section 3.1; the owner's adjustments from the Phase 0 renders | section 3.1, then his ears |
| 6 | ~~Clarity fixed at 0.5~~ **Settled 2026-10-25: 0.5** (below); the listening sheet's line 6 still plays it against off | | | |
| 7 | ~~Stop-the-tail button beside DECAY~~ **Settled 2026-10-25: kept, as the family's small icon at DECAY's corner** (below) | | | |
| 8 | Eight examples vs a small bank (decision 4: one per category, from the product's own bank) | Phase 4 | 8 categories with 12 presets (some have two); 8 presets, one each | 8 categories, 12 presets (`docs/factory-presets.md`) |
| 9 | The editor built on core's `PanelShell` | Phase 2 | PanelShell (Fluxwave's way); a second subclass of Clearlight's own editor base | PanelShell: the shared shell, and Clearlight can move to it later |
| 10 | Early reflections drawn in the display | Phase 3 | the glow alone; glow plus early-reflection specks | the glow alone first; specks if the owner wants more life |
| 11 | Blacklight in the Total Bundle | Phase 4 | in it; not in it | in it: the bundle lists every plugin (the owner does it in Moonbase) |
| 12 | This repo's role, and its visibility | now | design and docs here, code in Clearlight's repo (the edition); make it private | as stated, and private like the line's other repos (it is public today) |

### Settled

| Question | Decision | Date |
|---|---|---|
| Name | Blacklight, `-light` family | 2026-10-25 |
| Controls | SIZE and DECAY, plus OUTPUT's IN, OUT and MIX; nothing else visible | 2026-10-25 |
| Build | a Clearlight edition (one repo, an edition flag) | 2026-10-25 |
| Panel | a small black box with a minimal display | 2026-10-25 |
| Presets | a small factory set (about 8 to 12) named by use, plus user presets and the family's menu | 2026-10-25 |
| Price | low, about $19 to $29 | 2026-10-25 |
| Moonbase id | `blacklight`, created by the owner | 2026-10-25 |
| DECAY's range (open row 2) | **0.35 s to 30 s**, skew 0.35, default 4.5 s at mid-knob (face 0.502); the longer washes stay Clearlight's (0.35-60 s). The owner chose option 1 of three (0.35-30, 0.35-20, 0.35-60), asked on the desktop | 2026-10-25 |
| Clarity (open row 6) | **fixed at 0.5**, Clearlight's default; not a control. The owner chose option 1 of three (0.5, off, 0.7). Listening line 6 (0.5 against off, on the vocal) can still move it before Phase 1 | 2026-10-25 |
| The sound, first heard | *"The plugins sounds great"*: the owner, after trying the installed Phase 1 build (Clearlight `026c909`). The listening sheet's nine lines were not answered one by one | 2026-10-25 |
| Freeze | **in**: *"We need freeze button like the other reverbs (on the top bar)"*. A FREEZE header button like Clearlight's, on Clearlight's own `freeze` (the kill releases it, the tail reported infinite while it holds, never in a preset). Section 1's "no Freeze" is struck | 2026-10-25 |
| Panel width (open row 1) | **narrower than the family's 980, with the two knobs closer together**: *"The UI can be narrower with the two knobs closer to each other"*. A per-product ruling, as Dopplermotion's taller display is; the size comes as a mock | 2026-10-25 |
| Stop the tail (open row 7) | **the family's small icon next to DECAY**, not a pill: *"the stop tail button that is a small icon next to decay and not big pill"*. Core's `TailKillButton` at DECAY's corner, as Clearlight has it | 2026-10-25 |
| SIZE and DECAY's knobs | **the family's knob, large**, in place of the 3D knobs: *"The size and decay buttons are nice but they are too far from the family, maybe use something like the Dronevibe Weight?"* WEIGHT is core's `ParamKnob` drawn large (caption above, value below); its drawing is the same at Clearlight's core v0.25.0, so no core change. WEIGHT's two-colour ring with the 12 o'clock mark is its own and not used | 2026-10-25 |
| The glow's colours | Asked: *"I would like to see more colors (they can be faint) ... I like the fact that the glow is same color maybe something that is related to the level or intesity?"* Three ideas offered: 1 colour by loudness (one colour at a time, faint blue quiet, violet, warm amber loudest; Fluxwave's tape display colours by level the same way; no red, the family's "too hot"), 2 fed or fading (warm while sound pours in, blue once the room only rings), 3 a faint halo (today's core colour, which follows DECAY, plus a faint outer haze from blue to amber with loudness). **The owner: "4. default is 3 (let's add a menu option to select the type of visualizer)"**: all three are offered, the halo is the default, and the main menu chooses. The choice is a UI preference, kept per computer in the product's `.uisettings` like panel size and tooltips (core's `UiSettings`); published images and tests always draw the default (Ghostlight's old Show Spectrum Cloud preference, kept that way, once stripped the colour from every render made on its machine) | 2026-10-25 |
| The panel's layout (open row 1) | **800 x 490, as the mock** in `docs/panel-mockup.md` ("The approved panel"): the family header with FREEZE; the glow display the full width; one SPACE card with SIZE and DECAY as the family's large knobs close together, each value under its dial, the stop-the-tail icon at DECAY's corner; the OUTPUT card (MIX, IN, OUT, the meters, the MIX lock). The display's two corner readouts are dropped (each knob shows its value). 800 because the family header with FREEZE needs about 780 (`headerLayout`); 490 is the family's rows with no footer. The owner: *"1 as shown."* | 2026-10-25 |

## 11. Traps already known

- **Shared code means both editions:** every Clearlight change is rebuilt, tested and its installer rebuilt for both
  (Ghostlight Pro's lesson: stale installers shipped five times).
- **Guard declarations, not only uses:** an unused Clearlight parameter pointer in the edition warns under clang
  (Ghostlight's trap); every warning blocks a release (decision 18).
- **Leave Clearlight's editor out of the Blacklight target** (and vice versa): it names parameters the edition does
  not register; a test target given the wrong editor fails to link (Ghostlight's BankCheckLite).
- **Facts tooling for both editions**, or the edition's facts go stale silently (Ghostlight's
  `GhostlightLitePanelRender`).
- **Stored face values re-voice:** Size and Diffusion store face values, so changing the macro map after release
  re-voices every saved session. Free during beta (decision 6), deliberate after.
- **The clang check tool cannot read an edition helper function**; name `-Units` by hand (Ghostlight's note).
- **`design-decisions.md` §5f in Clearlight is stale** about the Mix law (the current law is core's
  `DryWetMix.h`, `dryExponent` 0.85).
- **The trial prompt:** tools that open the editor must not spend the machine's one-time prompt (Clearlight's guard,
  `moonbaseAutoPromptRefused()`).
