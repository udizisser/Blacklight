# Blacklight — dev log

> **First, in any session on this product:** read `docs/session-handover.md` (state of play, newest last) and
> `docs/design-brief.md` (the current-state snapshot). This file is a pointer and a chronological log, not a spec.
> The owner's rules hold in every window (`D:\Plugins Dev\CLAUDE.md`): answers before actions; number open items and
> ask one at a time; he does all the listening; nothing ships until Windows and macOS are both signed; no outside
> code; no release with a known issue.

**Blacklight** is a UDI DSP plugin: a very simple reverb on Clearlight's engine, SIZE and DECAY plus OUTPUT's IN,
OUT and MIX. It is a **Clearlight edition**: its code lives in Clearlight's repo (`udizisser/Clearlight`) behind
`CLEARLIGHT_EDITION_BLACKLIGHT`, as Ghostlight lives in Ghostlight Pro's. **This repo holds Blacklight's design and
documents**; open row 12 of the brief asks the owner whether it stays that way.

- **Identity:** name Blacklight · Moonbase `blacklight` (created 2026-10-25) · bundle `com.udidsp.blacklight` ·
  plugin code `Blkl` (proposed) · data folder `Blacklight` · presets `.blpreset` · installer AppId
  `{1B5000E2-C221-4FCD-9C32-9C5BE2DA4712}`.
- **Documents:** `docs/design-brief.md` (START HERE), `docs/panel-mockup.md`, `docs/factory-presets.md`,
  `docs/listening-sheet.md`, `docs/product-page-draft.md`, `docs/session-handover.md`, `docs/session-kickoff.md`.

## Status

Designed 2026-10-25; no code, no renders, nothing heard. Next: Phase 0 (voice the macro map) in Clearlight's repo,
on the desktop.

## 2026-10-25 — designed

Ideation with the owner in a cloud session, one question at a time: a simple reverb based on Clearlight with two
controls; IN, OUT and MIX beside them and nothing more ("if we had too many controls we're just recreating clear
light"); named Blacklight after "Black Box" turned out to be a known audio brand; a Clearlight edition; a small black
box with one quiet display; a small factory set; a low price. He created the Moonbase product and this repo. The
design was written from Clearlight's code at `38a16fd`, Ghostlight's edition pattern in MyReverb, and udidsp-hq's
rules: see `docs/session-handover.md` for what was read and the plan.
