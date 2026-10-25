# Blacklight — the factory set (draft, not auditioned)

The owner, 2026-10-25: a small factory set, about 8 to 12, named by use, plus user presets and the family's menu.
**Decision 4** asks for eight product-page examples, one per preset category, from the product's own bank, so the set
is drawn in **eight categories** (open row 8). A preset sets only SIZE, DECAY and MIX: IN and OUT stay 0 dB
(they are the user's, and the MIX lock holds them).

These are drafts from the macro map's physical story (`design-brief.md` section 3.1) and Clearlight's own preset
patterns. **Nothing here has been heard.** The owner voices them after Phase 0, and the ids freeze before they are
written into code.

| # | Category/Name | SIZE | DECAY | MIX | What it is for |
|---|---|---|---|---|---|
| 0 | Default | 55% | 4.5 s | 30% | the box as it opens: a medium hall |
| 1 | Vocals/Close Room | 30% | 1.2 s | 20% | a lead vocal that stays up front |
| 2 | Vocals/Ballad Hall | 70% | 3.2 s | 28% | slow vocals with a long, soft tail |
| 3 | Drums/Tight Room | 25% | 0.8 s | 22% | glue on a kit without a wash |
| 4 | Drums/Big Snare | 60% | 2.2 s | 25% | the classic large snare room |
| 5 | Guitars/Studio Room | 40% | 1.6 s | 25% | acoustic and clean electric |
| 6 | Keys/Piano Hall | 65% | 2.8 s | 30% | piano and electric piano |
| 7 | Strings/Concert Hall | 85% | 4.5 s | 35% | strings, brass, orchestral |
| 8 | Strings/Scoring Stage | 75% | 3.0 s | 30% | tighter, clearer orchestral space |
| 9 | Cinematic/Trailer Tail | 100% | 12 s | 40% | hits and risers that hang |
| 10 | Ambient/Endless Room | 100% | 28 s | 50% | pads and drones |
| 11 | Ambient/Soft Bed | 80% | 9 s | 40% | a cushion under a sparse arrangement |
| 12 | Sound Design/Small Long | 15% | 15 s | 35% | a tiny space with a long ring: the box's odd corner, kept clean |

Twelve presets plus Default, in eight categories: Vocals, Drums, Guitars, Keys, Strings, Cinematic, Ambient, Sound
Design. The eight examples take one each (the clips: Female Vocals, Drums, Electric or Acoustic Guitar, Piano,
Strings, Violin, Synth, Arp, as the line's renderers use).

**Checks they must pass** (BankCheck's harness, as Clearlight's): finite, silent on silence (-240 dBFS), under
0 dBFS on their category's clip, distinct from each other, and never listing a guarded parameter (`bypass`,
`panelBypass`, `killTail`).
