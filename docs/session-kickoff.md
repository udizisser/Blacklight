# Blacklight — session kickoff prompt

Paste this into a fresh Claude Code session on the Windows desktop (VS Code, `D:\Plugins Dev`).

---

We're starting the code for **Blacklight**, a new UDI DSP plugin: a very simple reverb on Clearlight's engine, with
only SIZE and DECAY plus OUTPUT's IN, OUT and MIX. It is a **Clearlight edition**: built from Clearlight's repo with a
build-time flag, the way Ghostlight is built from Ghostlight Pro's source. The design was done in a cloud session on
2026-10-25 and nothing has been coded, rendered or heard yet.

Read first, in this order:
1. `D:\Plugins Dev\Blacklight\docs\session-handover.md`: what was decided, what's open, and the step-by-step plan.
2. `D:\Plugins Dev\Blacklight\docs\design-brief.md`: the design; section 3.1 is the macro map (what SIZE and DECAY
   move underneath), section 10 the open decisions with leans.
3. `D:\Plugins Dev\Blacklight\docs\listening-sheet.md`: Phase 0's questions.
4. `D:\Plugins Dev\udidsp-hq\skills\new-product\SKILL.md`, and the `udidsp-decisions` and `udidsp-panel` skills.
5. `D:\Plugins Dev\Clearlight\docs\north-star.md` and Clearlight's `CLAUDE.md` (the log; skim it).
6. For the edition pattern: `D:\Plugins Dev\MyReverb\CMakeLists.txt` (`add_ghostlight_target`), and
   `GHOSTLIGHT_EDITION_LITE` in its `Source/`.

Pull all four repos first (Blacklight, Clearlight, MyReverb, udidsp-hq), and check Clearlight's core pin still builds.

Then start **Phase 0** only: `Source/BlacklightMacros.h` with its test, and the `BlacklightVoicing` render tool,
in Clearlight's repo on a new branch (not main). Render the listening sheet's files on the shared clips, matched in
loudness, and hand them to me with the sheet. Don't start the edition target (Phase 1) until I've listened and
answered.

Before you start, ask me the open decisions that block Phase 0 (DECAY's range, Clarity at 0.5): one question at a
time, numbered options, each explained as what I'd hear. Record every answer in the brief the same turn.

Standing rules: I do all the listening and DAW tests, so never launch a DAW. No outside code. Build the plugin with no
`--target` filter, and run the tests inside the desk's test lock. Commit on the branch as you go; ask before pushing
to any main.
