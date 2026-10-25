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

## 2026-10-25 — Phase 0 rendered, Phase 1 built (the desktop; this entry written by the session that resumed a stale one)

**What happened.** The desktop session settled DECAY's range (0.35-30 s) and Clarity (0.5) with the owner, built Phase 0
(Clearlight commit `3502a87`: the macro map `Source/BlacklightMacros.h`, its test, the voicing renders in Clearlight's
gitignored `listening/blacklight-phase0/`), then began Phase 1 and went stale with it uncommitted at 12:01. The owner,
asked which first (push, listen, or finish Phase 1), chose to **finish Phase 1 before his listening**; this session read
the uncommitted code line by line and finished it.

**Phase 0's listening is still open**: no verdict on any of the nine lines. The folder is on the desktop PC only
(`D:\Plugins Dev\Clearlight\listening\blacklight-phase0\`, README.txt names every file; the blind pairs' key is in
`_work\scratch\blacklight-phase0-key.txt`).

### Phase 1 as built (Clearlight repo, branch `blacklight-phase0`)

- **A runtime edition, not a compile-time one** (the desktop session's design, kept): `ClearlightAudioProcessor(Edition)`,
  the plug-in target choosing it by `CLEARLIGHT_EDITION_BLACKLIGHT=1`. So one test program runs both editions side by
  side. The eight parameters are registered and every other Clearlight parameter pointer is bound to a value the
  edition owns (Clearlight's default, or what the map hands on once a block). `renderStereo` is the same code for both.
- **CMake target `Blacklight`**: plugin code `Blkl` (open row 3, at the lean, provisional), bundle
  `com.udidsp.blacklight`, VST3/AU/AAX, no Standalone, `VERSION 0.1.0` provisional (`set(BLACKLIGHT_VERSION ...)`).
- **Presets**: `.blpreset`, `BlacklightPreset`, folder `Blacklight`, bank id `blacklight`; the twelve drafts of
  `docs/factory-presets.md` are in code as drafts (`blacklightPresetTable()`), unheard. MIX 30% (open row 4, at the lean).
- **The panel is Clearlight's editor with a Blacklight face** (the desktop session's choice, not the brief's 720 x 380
  design): the same header without FREEZE, a glow display in place of the visualizer, one SPACE card with two big
  3D-styled knobs and a STOP TAIL pill (`Source/blacklight/BlacklightControls.h`), OUTPUT without WIDTH, no footer.
  Its header quotes the owner: "a slick UI that feels functional but still keeps the family vibe. Maybe bigger
  buttons that feels more 3d. we can tweak the UI later." This is Phase 2's scaffold, not its design: nothing of it
  has been looked at, and PanelRender does not render it yet.
- **Gates** (`tools/run-tests.ps1`): `BlacklightMacrosTest`, `BlacklightSmokeTest` and `BlacklightHostBlockTest` (the two
  shared suites compiled as the edition), and **`BlacklightTest`**, the edition's own: the parameter list, the
  edition-equivalence check against Clearlight, Blacklight's own hash baseline, presets refused across editions, the
  MIX lock over every preset, the tail at the four corners, the kill, a session round trip, the display's feed. Every
  broken bar is caught (`--break`: ALL CAUGHT).
- **Installer**: `tools/build-installer.ps1 -Edition Blacklight` (AppId `{1B5000E2-...}` passed into the one .iss; a
  `-BuildDir` switch for build-pin). Dry run: `Blacklight-0.1.0-Windows-NoAAX.exe`, 4.3 MB, unsigned, named as a test
  installer, kept out of the Desktop folder.
- Built in `build-pin` against the core worktree at exactly v0.25.0 (the sibling core is at v0.27.0), 0 compiler
  warnings.

### What the gate found (corrected after the owner's restart, the same day)

**Equivalence first read 5 of 25, and the first diagnosis written here was wrong.** It blamed Damp (the map's Damp
reaching the chain unrounded while a host-set Damp passes through its skewed range). After the owner's restart freed the
sources, that patch went in and changed no hash at all, so it was reverted and the floats were measured instead
(`BlacklightTest --values`). **The cause is DECAY's two ranges**: Blacklight's 0.35-30 s and Clearlight's 0.35-60 s,
both skewed, round the same seconds to floats a unit in the last place apart (4.5 s is 4.4999995 in Blacklight,
4.5000005 in Clearlight). The 5 matching cases were exactly 0.35 s and 1 s, which both ranges hold exactly; Damp was
the same float in every row.

This is **not a defect in the edition** (the same seconds on a knob of another range, a relative change near 1e-7 in
the decay time), so the fix is the test's: the Clearlight twin is now handed bit for bit the floats Blacklight's chain
reads, the knobs' floats read off a shadow Blacklight instance and the map of those, stored into the twin's parameter
values. **25 of 25 identical**; Blacklight's own hashes never moved; the first baseline is written
(`Tests/chainhash-baseline-blacklight-windows.txt`, 37 cases); `--break` ALL CAUGHT.

The sources had been locked for hours by seven orphaned `cl.exe` processes from the stale session's build (12:03, a
hung compile); the session was not allowed to end them, and the owner's restart cleared them.

### Where Phase 1 ended (Clearlight `026c909`, branch `blacklight-phase0`, pushed)

- **Every gate green**: all 14 suites of Clearlight's runner, normal and `--break` (every break forced once). Clearlight's
  own ChainHash held, so Clearlight's sound is unchanged by the edition work.
- **0 compiler warnings** (MSVC), and **0 in the clang check** (the Windows stand-in for Apple's clang) on both plug-in
  targets and the three Blacklight test programs, after three small test-code fixes (two float `==` that Apple's clang
  would flag, one shadowed name).
- **Installed**: the Blacklight VST3 in `C:\Program Files\Common Files\VST3` (hash matches the build). Clearlight's
  installed 1.0.0 is left as it was.
- **Test installer**: `Blacklight-0.1.0-Windows-NoAAX.exe`, unsigned, in Clearlight's `build\installer` and `dist` only.
- **Nothing heard, nothing tried in a DAW, nothing on the Mac.**

### Left for later, by name

- The compact 720 x 380 panel of the brief (open row 1) against the scaffold now built; PanelRender for Blacklight.
- HostReadinessTest, KillTailTest, ChainHash, BankCheck and CpuBenchmark compiled as the edition (they set Freeze,
  Knee or the stages by id); what Blacklight needs of them is in BlacklightTest. CPU at the four corners is unmeasured.
- Phase 0's renders came from Clearlight's processor given the map's values by host writes, so their DECAY is
  Clearlight's float for the seconds, a unit in the last place from Blacklight's; inaudible, so the renders stand.
- The Mac: nothing built or compiled there yet (Apple's clang may warn where MSVC does not).
- udidsp-hq rows (status, ids, line-status, the release scripts' tables) wait for the first release, as before; the
  install script `work/scripts/install-vst3.ps1` already lists Blacklight (and searches `build-pin`).
