# Blacklight — panel mockups (ASCII, before any panel code)

The owner's choice (2026-10-25): **a small black box with a minimal display**. These mockups go to him before the
scaffold is coded (`new-product` section 1: ASCII mockups before panel code). Sizes are at 100%; core's `panelSize`
gives 75/100/125/150%.

## The approved panel, 800 x 490 (the owner, 2026-10-25: "1 as shown.")

Shown to the owner after his rulings on the first build (FREEZE in the header; narrower, the knobs closer; the
stop-the-tail as the small icon at DECAY; the family's knob, like Dronevibe's WEIGHT; a faint halo as the default
glow, the type chosen in the menu). 800 wide because the family header with FREEZE, presets, the visualizer button,
the menu, POWER and the licence badge needs about 780 (`headerLayout`); 490 tall, the family's rows with no footer.
Ten pixels a column.

```
+------------------------------------------------------------------------------+
| BLACKLIGHT      [ FREEZE ]      TRIAL      [<][  Default       ][>]  v  =  o |
| UDI DSP - REVERB                                                             |
| ---------------------------------------------------------------------------- |
| .--------------------------------------------------------------------------. |
| |                                                                          | |
| |                     .    .    .    .    .    .    .                      | |
| |             .         ,~~~~~~~~~~~~~~~~~~~~~~~~~~.         .             | |
| | --+-----+-----+-----(==============  glow  ==============)-----+-----+-- | |
| |             .         `~~~~~~~~~~~~~~~~~~~~~~~~~~'         .             | |
| |                     .    .    .    .    .    .    .                      | |
| |                                                                          | |
| '--------------------------------------------------------------------------' |
| .- SPACE -----------------------------------------------. .- OUTPUT -------. |
| |                                                       | | MIX       [L]  | |
| |               SIZE              DECAY                 | | .---.    | |   | |
| |              .-----.           .-----.                | |(  /  )   | |   | |
| |             /       \         /       \               | | '---'    | |   | |
| |            |    /    |       |     \   |              | |  30 %    | |   | |
| |             \       /       x \       /               | | IN   OUT | |   | |
| |              '-----'           '-----'                | | o     o        | |
| |                55 %             4.5 s                 | |0 dB  0 dB      | |
| |                                                       | |                | |
| '-------------------------------------------------------' '----------------' |
|                                                                              |
+------------------------------------------------------------------------------+
```

- **SIZE and DECAY** are core's `ParamKnob`, large, close together, each value under its dial (WEIGHT's look without
  its two-colour ring).
- **x** at DECAY's corner is core's `TailKillButton`, as on Clearlight's DECAY.
- **The dots** round the glow are the faint halo, the default type (Halo); the main menu's Visualizer list also offers
  Loudness and Fed or fading. The choice is kept per computer (`UiSettings`); renders and tests draw the default.
- **[L]** is the MIX lock; **v, =, o** the visualizer button, the menu and POWER; **| |** the IN and OUT meters.
- **Dropped from the first build, at the owner's word:** the SIZE and DECAY readouts in the display's top corners (each
  knob shows its value), the 3D knobs, the STOP TAIL pill.

The drafts below (A to C, 720 x 380, written before any code) are superseded by this panel.

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
