# Blacklight — Phase 0 listening sheet (the owner's)

Written before anything is rendered or heard, so the pass rules can't follow the result. The renders come from
`BlacklightVoicing` in Clearlight's repo: Clearlight's real processor with the macro map applied
(`design-brief.md` section 3.1). Every file in a pair is matched in loudness first, so a pair compares sound,
not level.

**Clips:** Female Vocals, Drums, Piano (the line's shared clips). MIX 30% unless a line says otherwise.

| # | Question | Files | Listen for | Pass rule (decided now) |
|---|---|---|---|---|
| 1 | Does the grid sound like one reverb at sixteen sizes? | SIZE 15/40/65/90% x DECAY 0.6/2/6/20 s, on each clip | every file reads as the same character, growing; no corner sounds broken, metallic or fluttery | all 16 usable on all three clips; a "broken" file fails its corner |
| 2 | Small room, long decay (the odd corner) | SIZE 0%, DECAY 30 s | metallic ringing, flutter, a tank sound | clean enough to use as a sound-design effect |
| 3 | Big room, short decay | SIZE 100%, DECAY 0.35 s | a gated-hall feel vs a mess of echoes | reads as a big space that stops |
| 4 | Is Damp following DECAY right? | DECAY 12 s with the map's Damp (5.0 kHz) vs fixed 7 kHz | the long tail tiring the ear vs going dull | the mapped one is preferred or equal |
| 5 | Is Early following SIZE right? | SIZE 85%, Early from the map (0.33) vs 0.5 | the source's closeness vs being washed away | the mapped one keeps the voice clear and the space big |
| 6 | Clarity 0.5 vs off (open row 6) | the default on Female Vocals | words staying clear in a long tail | 0.5 kept unless off is preferred |
| 7 | Pre-delay growing with SIZE | SIZE 90%, mapped 36 ms vs fixed 16 ms | the attack separating from the hall vs smearing | the mapped one is preferred or equal |
| 8 | The default, as a first impression | SIZE 55%, DECAY 4.5 s, MIX 30% on all three | "that's a good reverb" without touching anything | yes on all three |
| 9 | A knob sweep, live (later, in the DAW, Phase 1) | SIZE 0 to 100% and DECAY 0.35 to 30 s while playing | zipper, clicks, pitch wobble, a jump in level | smooth |

**Report:** per line, pass / fail / a note. A failed line names the setting to move, and the map changes before
Phase 1.

**Closed 2026-10-25, by the owner.** After trying the installed Phase 1 build he said *"The plugins sounds great"*,
and asked whether that settles the knob mapping, he chose *"1"*: keep it as it is and close this sheet. The nine lines
were not answered one by one; the map stands as `design-brief.md` section 3.1.
