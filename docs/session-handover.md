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

## 2026-10-25 (night) — the UI, the guide, the product page and the blog post

**The owner's words, in order:** after trying the Phase 1 build, *"The plugins sounds great"*, then the UI rulings (FREEZE
in the header, narrower, the stop-the-tail icon at DECAY, the family's knobs, more colours in the glow) and *"4. default
is 3 (let's add a menu option to select the type of visualizer)"*, *"1 as shown."* (800 x 490), *"1"* (the map settled);
then *"Work on user guide, website product page, blog post, Price is 29 USD start."* and *"I mean do it all"*. Seeing the
built panel: *"seems like the plug-in can be even narrower or possibly add another knob just to fill in the gap"*, *"another
option to make the knobs bigger"*; offered four ways with the header's floor (about 727 px), he chose **MIX joins SIZE,
DECAY**, and *"but still, size and decay should be bigger than the other knobs"*. All recorded in `design-brief.md`
section 10, Settled; the as-built panel is in `panel-mockup.md`.

### Built (Clearlight's branch `blacklight-phase0`, `63b5079` and `dc22460`, pushed)

- **FREEZE**: Clearlight's own, nine parameters. The 37 earlier hashes held; one new equivalence case (Freeze held 2 s to
  4 s), 26 of 26 identical to Clearlight.
- **The panel, 800 x 490**: SIZE and DECAY at 140 px (the family's knob drawing, its 96 px cap lifted), MIX at 80 px with
  its padlock, every dial's centre on one line; OUTPUT holds IN over OUT and the meters; the display without its corner
  readouts. Clearlight's own panel renders unchanged.
- **The glow's three types**: Halo (the default), Loudness, Fed or Fading, from the menu's Visualizer list; kept per
  computer in `Blacklight-visualizer.uisettings` beside the `.uisettings` (core's `UiSettings` has no key for it; a core
  key waits for a core tag). Published images and tests draw the halo.
- **Checks**: `BlacklightTest` [10] Freeze and [11] the colours as drawn; `BlacklightPanelRender` (the panel's checks, the
  guide's pictures, the facts); all 15 suites green normal and `--break`; clang 0; full build 0 warnings.
- **CPU** (`BlacklightCpuBenchmark`, measured): 3.64% of a core at the defaults, 3.73% at SIZE 100% DECAY 30 s, 0.02% in
  silence, 2.2% holding a tail under Freeze; the panel open adds about a tenth of a point and changes no output hash.
- **Installed**: the Blacklight VST3 (build 274, hash matches). Test installer `Blacklight-0.1.0-Windows-NoAAX.exe`,
  unsigned, in Clearlight's `build\installer` and `dist` only (the AAX needs the owner's iLok to be PACE-signed).

### The guide, the page and the post

- **Guide**: Clearlight's `docs/blacklight-user-guide.md` (Ghostlight's edition layout), exported by
  `tools/export-site-guide.ps1 -Edition Blacklight`. Every number from the facts, the map, the tests or the benchmark.
- **Product page and blog post**: the website's branch **`blacklight`** (`2744931`, pushed, **not merged**): the page
  unlisted (`listed: false`, coming soon, $29, the Moonbase id held back until the storefront sells it), the post
  "Blacklight or Clearlight? One engine, two ways to drive it" as `draft: true`, its pictures from
  `tools/blog-animations/blacklight.py` (the glow's animation from frames the plugin renders itself). The site deploys
  on every push to `main`, so merging is the owner's release step.

### Waiting on the owner

- His look at the new panel and the three glow types (installed); his listening to the factory presets (drafts, unheard:
  the guide's presets chapter follows them).
- Moonbase: ~~trials switched off~~ **trials on since the same night** (the owner: "The moobase is now set with trials";
  read back: 14 days, account required, 5 activations a licence); the product is not yet purchasable; adding it to the
  Total Bundle (open row 11).
- Open rows 3 (`Blkl`), 4 (MIX 30%), 8 (examples vs bank), 10 (early-reflection specks), 12 (this repo is public).
- The eight dry/wet examples for the page (decision 4) wait for the voiced bank. The Mac build, signing, and the AAX.
- A session saved with the first build (980 wide) reopens at 122% of the new 800: Panel Size 100% puts it right.

**Published the same night for review** (the owner: "you can already make them on the website.. it's easier to review
and we can always change later"): the website's `main` at `ef380b9`, deployed. The page and guide are live at
udidsp.com/products/blacklight/ (coming soon, $29, still listed nowhere, the Moonbase id still held back) and the post
at udidsp.com/blog/blacklight-or-clearlight/ (no longer a draft, so /blog lists it).

## 2026-10-25 (late) — Blacklight 1.0.0 signed on both platforms

The owner: *"let's finalize windows including Axx, signing etc, in parallel work on the macos in it's session"*; asked first, he
gave the version (**1.0.0**), kept the plugin code (**`Blkl`, now frozen**) and moved the iLok between the PC and the Mac mini.

- **Both platforms at Clearlight `5fe45c5`, build 277** (branch `blacklight-phase0`). Windows: `Blacklight-1.0.0-Windows.exe` Valid (UDI
  AUDIO), the AAX PACE-signed and verified, VST3 and signed AAX installed, pluginval 10 clean twice, full build 0 warnings. Mac (the
  Blacklight session on the mini): `Blacklight-1.0.0-macOS.pkg`, notarized (Accepted, 0 issues), `verify-mac.sh` 1 of 1, auval PASS.
- **A bug the Mac caught**: BlacklightTest [11e] failed on Apple silicon only. The box's accent was an inline global copied from
  `theme::ghost` at start-up, made there before the theme's constant, so it came out empty: the halo lost DECAY's colour and SIZE's,
  DECAY's and the stop icon's blue went with it. Now `boxAccent()`, read when drawn (`5fe45c5`). The first signing at `799ccba` is
  superseded.
- Facts at 1.0.0, build 277, committed after both packages (the website's PLUGIN-FACTS-BLACKLIGHT.md updated).
- **Left:** the owner's tests on both platforms (he said he will run them now); the Mac hash baseline and the repo's Mac installer
  script for the edition (the Mac session holds both: commits after the packages); pluginval on the Mac; merging `blacklight-phase0`
  into Clearlight's `main` (asked, never assumed); Moonbase uploads, purchasable, the Total Bundle, and the page's release switches
  (available, listed, the Moonbase id), all his.
