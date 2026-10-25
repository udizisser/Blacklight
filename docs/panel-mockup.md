# Blacklight — panel mockups (ASCII, before any panel code)

The owner's choice (2026-10-25): **a small black box with a minimal display**. These mockups go to him before the
scaffold is coded (`new-product` section 1: ASCII mockups before panel code). Sizes are at 100%; core's `panelSize`
gives 75/100/125/150%.

## A. The panel, 720 x 380

```
+----------------------------------------------------------------------------------------------+
| BLACKLIGHT                         [ START TRIAL ]           < Default            >  [=]  (O) |  header 44
|  SIMPLE REVERB                                                                                |
+----------------------------------------------------------------------------------------------+
|                                                                         | OUTPUT             |
|  .................................................................     |                    |
|  :                                                               :     |       .--.         |
|  :              . . .  ~~~~~~~~~~~~~~~~~~~~~~~~~~  . . .          :     |      (    )        |  display 130
|  :                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~           :     |       '--'  [lock] |
|  :              . . .  ~~~~~~~~~~~~~~~~~~~~~~~~~~  . . .          :     |       MIX          |
|  :...............................................................:     |       30 %         |
|                                                                         |                    |
|  SPACE ---------------------------------------------------------------- |   .-.     .-.  | | |
|                                                                         |  ( IN )  (OUT) | | |
|               .-----.                         .-----.                  |   '-'     '-'  | | |  card 150
|              /       \                       /       \                 |  0.0 dB  0.0 dB| | |
|             |  SIZE   |                     |  DECAY  |                 |                | | |
|              \       /                       \       /                 |                M M |
|               '-----'                         '-----' [x]              |                    |
|                55 %                            4.5 s                   |                    |
+----------------------------------------------------------------------------------------------+
  left 504 (display over SPACE)                                    gap 12   OUTPUT 172 (MIX, IN/OUT, meters, padlock)
```

- `[=]` the menu, `(O)` POWER, `< ... >` the preset field. **No visualizer button**: the display does not collapse.
- `[x]` on DECAY is the stop-the-tail corner button (open row 7), placed as Clearlight places it.
- `[lock]` is the MIX lock padlock beside MIX's readout (decision 11).
- `M M` are the IN and OUT meters in the 46 px strip.
- No footer row (decision 3).

## B. The display, three states

```
silence / bypass                 a sound playing, SIZE 30%           SIZE 90%, DECAY 20 s, 3 s after the source stops
.......................         .......................             .......................
:                     :         :                     :             :                     :
:                     :         :      ~~~~~~~~~      :             :  ~~~~~~~~~~~~~~~~~  :   (wide, dimmer, deeper colour,
:                     :         :    ~~~~~~~~~~~~~    :             :  ~~~~~~~~~~~~~~~~~  :    still fading)
:                     :         :      ~~~~~~~~~      :             :  ~~~~~~~~~~~~~~~~~  :
:.....................:         :.....................:             :.....................:
  dark: nothing is drawn          narrow, bright                      the glow hangs as long as the tail does
```

- **Width = SIZE** (the knob's smoothed value).
- **Brightness = the real wet level** the processor publishes (-60 dBFS dark to -6 dBFS full), so it fades with
  the tail's own decay.
- **Colour** deepens as Damp falls (long DECAY).
- Optional (open row 10): faint specks either side for the early reflections, arriving before the glow builds.

## C. Smaller scale, 75% (540 x 285)

Everything scales together from the width (`PaintedPanel`); nothing is laid out twice. The header must still fit
BLACKLIGHT, the 80 x 18 badge, the preset field, the menu and POWER at 540 px: check it in PanelRender at 75%.
