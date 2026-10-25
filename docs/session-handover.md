# Blacklight — session handover

State of play, newest entry last. The brief (`design-brief.md`) records the design; this file records where things
actually stand.

## 2026-10-25 — the design, written in a cloud session (no code)

### What was decided with the owner, one question at a time

| # | Decision | His words / choice |
|---|---|---|
| 1 | A simple reverb on Clearlight's engine | "a very simple reverb based on clearlight which has two controls size and decay" |
| 2 | Controls: SIZE, DECAY, and OUTPUT's IN, OUT, MIX; no more | "if we had too many controls we're just recreating clear light" |
| 3 | Name: Blacklight (the `-light` family). "Black Box" was dropped: a known audio brand (Black Box Analog Design) | "We can call it blacklight" |
| 4 | Build: a Clearlight edition | choice 1 |
| 5 | Panel: a small black box with a minimal display | choice 2 |
| 6 | Presets: a small factory set (8-12) by use, plus user presets | choice 1 |
| 7 | Price: low, about $19-29 | choice 2 |
| 8 | Moonbase product `blacklight` created; GitHub repo `udizisser/Blacklight` opened | the owner, 2026-10-25 |

### What was written (this repo)

- `docs/design-brief.md`: the whole design, in the template's eleven sections. **The macro map** (section 3.1)
  is the heart of it: what SIZE and DECAY move underneath, with formulas and tables.
- `docs/panel-mockup.md`: ASCII mockups of the 720 x 380 panel and the display.
- `docs/factory-presets.md`: twelve draft presets plus Default in eight categories.
- `docs/listening-sheet.md`: Phase 0's nine questions, pass rules written before any render.
- `docs/product-page-draft.md`: a first draft of the page and tagline (not for publishing).
- `docs/session-kickoff.md`: the prompt for the desktop session.

### What was read to write it (at these commits)

- Clearlight `38a16fd` (1.0.0, core pin v0.25.0): the signal chain (`PluginProcessor.cpp` `renderStereo`),
  `clearlight::ranges` (`PluginProcessor.h:61-234`), the 45 factory presets (`factoryPresetTable`, `.cpp:641-837`),
  the tail (`getTailLengthSeconds`, `.cpp:1928-2000`), the rest, the kill, the editor, the tests.
- MyReverb (Ghostlight Pro / Ghostlight): how one source builds two editions: `add_ghostlight_target`
  (`CMakeLists.txt:204-308`), `GHOSTLIGHT_EDITION_LITE`, per-edition presets (`PresetManager.h:29-114`), Moonbase id,
  installer `-Edition`, per-edition tests and CPU baselines.
- udidsp-hq: `new-product`, `udidsp-decisions`, `udidsp-panel`, `release-line`, the product brief template.

### Nothing is built, rendered or heard

Every number in the macro map is a proposal from Clearlight's presets and a physical story. Phase 0 is the owner's
listening on renders a desktop session makes.

### Open, the owner's (asked one at a time; the brief's section 10 has options and leans)

1. The compact panel as an exception to `udidsp-panel` (720 x 380, no visualizer button, no footer).
2. DECAY's range (lean 0.35-30 s).
3. The plugin code (lean `Blkl`).
4. MIX's default (lean 30%).
5. The macro map's numbers (after Phase 0).
6. Clarity fixed at 0.5.
7. The stop-the-tail button beside DECAY.
8. Eight categories, twelve presets, for decision 4's eight examples.
9. The editor on core's `PanelShell`.
10. Early-reflection specks in the display.
11. Blacklight in the Total Bundle.
12. **This repo is public**, while the line's repos are private: make it private? And its role: design here, code
    in Clearlight's repo.

Also to settle in udidsp-hq later: Blacklight's rows (decision 6 status, decision 9 id, `line-status`, the release
scripts' tables), at its first release (`release-line` section 5, entry C).

### The plan for the desktop session

All code goes into **Clearlight's repo** (`D:\Plugins Dev\Clearlight`), on a branch, not main, until the owner says.

**Phase 0 (first):**
1. `Source/BlacklightMacros.h` (JUCE-free): `struct Settings { size, decaySeconds, predelayMs, diffusion, early,
   spread, focus, dampHz; }` and `Settings map(float size, float decaySeconds)`, plus the fixed values as named
   constants with a comment each. A unit test `BlacklightMacrosTest` (monotonic, inside floors and ceilings on a
   101 x 101 sweep; `--break` pushes Early past its face ceiling).
2. `Tests/BlacklightVoicing.cpp` (`EXCLUDE_FROM_ALL`): Clearlight's real processor, parameters set through the map
   and the fixed values, rendering the listening sheet's files on the shared clips into a gitignored
   `listening/blacklight-phase0/`, each pair loudness-matched. Hand the folder to the owner with the sheet.

**Phase 1 (after his Phase 0 verdict):**
3. CMake: turn Clearlight's `juce_add_plugin` into a helper (`add_clearlight_target`, Ghostlight's
   `add_ghostlight_target` pattern) and add target `Blacklight`: product name `Blacklight`, bundle
   `com.udidsp.blacklight`, plugin code (open row 3), `CLEARLIGHT_EDITION_BLACKLIGHT=1`, the edition's editor
   sources; no Standalone.
4. `PluginProcessor`: register only the eight parameters under the flag; guard every other parameter pointer (and
   its declaration); in `renderStereo`, take the edition's values from `blacklight::map()` once per block where
   Clearlight reads its parameters; Moonbase id `blacklight`; `mixLockIds()` unchanged.
5. `PresetManager.h`: the edition's extension, root tag, folder and bank id; refuse the other edition's files.
6. Tests for both editions: SmokeTest, HostReadinessTest, KillTailTest, HostBlockTest, BankCheck (Blacklight's
   own `banks/` check skipped until a bank exists), ChainHash with `chainhash-baseline-blacklight-windows.txt`,
   CpuBenchmark with its own hashes, and **`BlacklightEquivalence`**: the edition bit-identical to Clearlight given the
   mapped values. `tools/run-tests.ps1` lists the pairs.
7. Installer: `tools/build-installer.ps1 -Edition Blacklight` with the brief's `AppId`; the Mac script reads both
   targets.

**Then** the compact editor (Phase 2-3) and the ship work (Phase 4), per the brief.

### Rules the desktop session must keep

The owner does all listening and every DAW test; never launch a DAW. One numbered question at a time, explained as
what you hear. Record every decision the turn it is made. No outside code (decision 19). Nothing ships until Windows
and macOS are both signed. Read `D:\Plugins Dev\udidsp-hq\skills\new-product\SKILL.md` and Clearlight's
`CLAUDE.md` (its log) and `docs/north-star.md` first.
