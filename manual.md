# Twin Pad Sequencer v11: Manual

Twin Pad Sequencer is a step sequencer that runs in your web browser and can be controlled via the web interface or up to two Novation Launchpads. It sends MIDI notes to a synthesizer or DAW. This manual first explains how to use it, then ends with a detailed reference.

**Contents**
1. [Introduction](#1-introduction)
2. [Getting started](#2-getting-started)
3. [The screen](#3-the-screen)
4. [Basic use](#4-basic-use)
5. [Working with the sequence](#5-working-with-the-sequence)
6. [Performance tools](#6-performance-tools)
7. [Saving and sync](#7-saving-and-sync)
8. [Troubleshooting](#8-troubleshooting)
9. [Reference](#9-reference)

## 1. Introduction

### What it does

- It has **4 tracks**. Each track has **8 note lanes** and **16 steps**.
- The two Launchpads together show one track at a time. **Pad A** shows steps 1–8 and **Pad B** shows steps 9–16. Each pad's 8×8 grid is steps across and lanes (notes) up.
- **One or two Launchpads:** you can use **one Launchpad or two**. With two, each pad shows half of the sequence. With one, set up only Pad A and leave Pad B on "none"; the pad then shows steps 1–8, and **Swap** (top row button 7) switches it to steps 9–16 together with the second set of controls (tempo, scale, root, lane buttons). The screen always shows both pads.
- Everything you can do on a Launchpad you can also do on the screen, so the app also works with no Launchpad at all (mouse or touch only).

### Requirements

- **Browser:** Chromium based browser like Chrome or Edge. Support for the Web MIDI API, including SysEx is essential. For iOS the MIDIWeb app is recommended.
- **Hardware (optional):** one or two Launchpads: LP Mini MK3, LP X, Launchpad Pro / Pro MK2, or an original Launchpad (X-Y or Drum Rack layout). Different models can be mixed.
- **Instrument output:** a MIDI input on a synthesizer, DAW or virtual MIDI port, chosen separately for each track. The app makes no sound by itself.
- **How to open it:** from disk or from `localhost`. The page asks for MIDI access when you click **Connect MIDI**.

### Basic ideas

- **Selected track:** the pads and the settings row always edit the *selected track*. The selected track's button is the brightest in the Pad A right column.
- **Lane:** one row of notes. The bottom row is the lowest note.
- **Step:** one 16th note. The default length is 16 steps, one bar.
- **Modes:** several functions (mute, clear, extend note, velocity, gate, probability, stepping mode, loop setting) work as *modes*. 
- **Toggle buttons:** Mute, Mode, Extend, ♭, ♯, Vel, Gate and Prob (the views) change state only when you press them: they stay on until you press the same button again, however many targets you use. An active toggle button **flashes red**. Buttons that gain a second function while it is on keep their colour and **flash** instead (Mode: T1–T4, Mute, Prob, Gate, Vel, BPM±, Scale±, Root± and the lane buttons; Mute: Extend, T1–T4 and the lane buttons; Oct, Scale, Root and Div selection: T1–T4, Extend and (Oct only) the lane buttons; ♭/♯: the lane buttons). The note grid never flashes. Pressing another toggle button switches the active one off.

------------------------------------------------------------------------

## 2. Getting started

1.  Open the page in your browser.
2.  Click **Connect MIDI** and allow MIDI access, including SysEx. The status line reports how many inputs and outputs it found.
3.  Open **MIDI devices** if it is collapsed (it starts closed on small screens) and set:
    - **Pad A input / output** and **Pad B input / output**: choose the **MIDI** port of each Launchpad, not the **DAW** port. With two identical pads, pick the first for A and the second for B. With a single Launchpad, set only Pad A and leave Pad B on "none".
    - **Pad A model / Pad B model**: choose the matching Launchpad model (see [Launchpad models](#launchpad-models)). The default is LP Mini MK3.
    - **Instrument output** is not set here. Each track has its own, chosen in the settings row (see [Choosing notes and sounds](#choosing-notes-and-sounds-for-a-track)).
    - **Clock input**: optional, only if you want to sync to another device (see [Sync](#external-clock-and-start--stop)).
4.  The pads light up. Press **Play** (top row, first button of Pad A, or on screen).
5.  Choose a track and click or press notes in the grid. Notes are sent on the channel set for that track.

On the first connect the app sets up the devices for you: it picks the Launchpad ports it finds (first one for Pad A, second one for Pad B), guesses LP X or LP Pro from the port name, and gives every track an instrument output that is not a Launchpad (you can change it per track). **Automatic setup never uses a port whose name contains "DAW"** (and it skips Launchpad "standalone" ports). You can still choose any port yourself in the dropdowns. Your choices are saved and restored the next time the page opens, including the automatic reconnect.

### Launchpad models

| Choice           | Use it for                                              |
|------------------|---------------------------------------------------------|
| **LP Mini MK3**  | Launchpad Mini MK3 (default).                           |
| **LP X**         | Launchpad X.                                            |
| **LP Pro**       | Launchpad Pro or Pro MK2. The Pro MK3 is not supported. |
| **LP X-Y Mode**  | The original Launchpad, in its X-Y layout.              |
| **LP Drum Mode** | The original Launchpad, in its Drum Rack layout.        |

The two original-Launchpad choices use all eight top-row buttons (CC 68h–6Fh) and have only red and green LEDs with four levels each. Blue and cyan colours therefore show as green, the very dark blue of the loop range is hard to see, and there is no logo LED. Everything works the same otherwise.

------------------------------------------------------------------------

## 3. The screen

From top to bottom:

1.  **Title row:** the **Connect MIDI** button and a status line with messages and hints.
2.  **MIDI devices:** the collapsible device and model selectors.
3.  **Top bar (stays visible while scrolling):**
    - **BPM** tempo field.
    - **External clock** toggle button.
    - **Length** of the sequence in steps.
    - **Save All**, **Save MIDI**, **Save Ptn** and **Load** buttons.
4.  **Settings row** for the selected track: **Scale**, **Root**, **Octave**, **Tempo ÷**, **Instrument output**, **Channel**, then **RND** (randomize).
5.  **The two pads.** Each pad on screen copies a Launchpad:
    - a **top row** of 8 buttons with a small cell for the **logo** at its right,
    - the **8×8 note grid**,
    - a **right column** of 8 buttons beside the grid.

    Buttons show the same functions and the same LED colours as the hardware, and you click or tap them like the hardware buttons.
6.  **Hardware controls:** a collapsible help section at the bottom.
7.  **RESET** (bottom right corner): after a confirmation it clears everything (patterns, MIDI and all other settings) and deletes the autosaved data, so the app starts as on its first load. Save your work first if you want to keep it.

On a phone in portrait, the two pads are stacked. On a tablet or desktop they sit side by side. On a phone in landscape both pads fit next to each other under the top bar.

------------------------------------------------------------------------

## 4. Basic use

### Playing and stopping

- **Play / Stop:** the first button of Pad A's top row. On screen the label changes between Play and Stop.
- **Tempo:** the **BPM** field (30–300), or buttons 5 and 6 of Pad A's top row (BPM− and BPM+). Each press changes the tempo by 1 and shows it for 2 seconds (see [Character display](#character-display)). Hold a button for more than 1.5 seconds and the tempo changes by 10 every 0.5 seconds until you release it. With **External clock** on, the buttons change nothing and only show the measured tempo.

### Entering notes

- Press a pad in the grid to switch a note on or off. Row = lane (pitch), column = step.
- The note takes the colour of the selected track. The playhead is a lit column that moves across the grid.
- **Pad A** covers steps 1–8 and **Pad B** covers steps 9–16.

### Choosing a track

- Press **T1** to **T4** in the Pad A right column (the top four buttons). The track colours are amber, cyan, magenta and lime. The selected track is brightest.
- The grid, scale, octave and channel then show and edit that track.

### Choosing notes and sounds for a track

- **Scale** (per track): the **Scale** dropdown or Pad B top row buttons 3 and 4 (select previous / next, then apply; see [Selection buttons](#selection-buttons-oct-scale-root)). Lanes are scale degrees from the root up. **Drums** maps the 8 lanes to General MIDI drum notes.
- **Root** (per track, shifted from a global root): the **Root** dropdown (it shows the selected track's root, moves all tracks together) or Pad B top row buttons 5 and 6, which select a root and apply it to a track or all tracks (see [Selection buttons](#selection-buttons-oct-scale-root)). On a track set to **Chromatic** those buttons are ♭ and ♯ (see [Chromatic scale](#chromatic-scale-sharps-and-flats)); press **Mode** first to get the root selection there. Lanes that are the root note have a dark green background (not in Drums). **Octave carry:** when a root passes from B to C the track's octave goes up by 1, from C to B down by 1 (limit ±3, Drums excluded).
- **Octave** (per track, −3 to +3): the **Octave** dropdown, or Pad B top row buttons 1 and 2 (**Oct−/Oct+**), which select an octave change and apply it to a note, step, lane, track or all tracks (see [Selection buttons](#selection-buttons-oct-scale-root)). The sounding octave shift of a note is the sum of four tiers: note + lane + track + All (±3 each). Each tier value is **absolute**, not cumulative: applying +1 twice gives +1, and 0 resets that element. Note-specific values reset when the note is switched off and on again.
- **Tempo ÷** (per track, default 16): the note value of one step of the track. There are 20 divisions from 32nd notes to two whole notes, with triplets (T) and dotted notes (D), from fast to slow: 32T, 32, 16T, 32D, 16, 8T, 16D, 8, 4T, 8D, 4, 2T, 4D, 2, *1T, 2D, *1, *2T, *1D, *2. Whole notes are shown with an asterisk in front (*1 = one whole note, *2 = two whole notes); 2 is a half note. 16 (sixteenth notes) is the normal speed. Notes and gates stretch with it. Tracks stay locked to the same clock, and the divider never restarts playback. **Div−/Div+ buttons** (Pad A top row 3 and 4, a selection like Oct): each press selects the next faster (−) or slower (+) division and shows it for 2 seconds on both pads; then press a track button T1–T4 to assign it to that track, or **Extend** (All) to assign it to all four tracks, drums included.
- **Options (LED brightness, MIDI channel):** press **Mode**, then **BPM−** or **BPM+** (Pad A top row, buttons 5 and 6, which show Opt). The bottom row of Pad A becomes a horizontal **brightness slider for the Launchpad LEDs** (leftmost pad dimmest, rightmost pad full). The four grid rows above the middle become **MIDI channel sliders**, one row per track (top row = T1 … fourth row = T4), spanning both pads: the leftmost pad is channel 1 (Pad A 1–8), the rightmost channel 16 (Pad B 9–16). The rest is dark. T1–T4 select the track. The on-screen pads are not dimmed. The setting is saved with the MIDI settings. Press **Mode** again to leave.
- **Instrument output** (per track): the MIDI output this track plays to. Choose a different device per track to play several instruments at once. The default is the first port that is not a Launchpad. Changing it sends all-notes-off to the old device.
- **Channel** (per track, 1–16): the **Channel** dropdown. Drums use channel 10 by default.

### Selection buttons (Oct, Scale, Root)

**Oct−/Oct+** (Pad B top row 1–2), **Scale◀/▶** (3–4), **Root−/+** (5–6) and **Div−/Div+** (Pad A top row 3–4) first **select** a value and then apply it to a destination.

1. Press the button once or repeatedly. It flashes red and the selection is shown on Pad B's note grid (and on screen) as two characters. **Oct:** sign (+, − or blank) and digit, starting at 0 (range −3 to +3). **Scale:** abbreviation, starting at the selected track's scale. **Root:** note, starting at the selected track's root. **Div:** note value, starting at the selected track's divider; + is slower, − is faster (no wrap-around).
2. Press a destination. The selection is applied and the selection ends.

| Destination | Oct | Scale | Root |
|---|---|---|---|
| A note | That note | – | – |
| An empty pad (step) | All notes of that step | – | – |
| A Pad B lane button | The whole lane | – | – |
| T1–T4 | That track | That track | That track |
| **Extend** (shows All) | All tracks | All tracks | All tracks, moved by the same interval |

Octave values are absolute (see above). Elements with a non-zero value are marked: notes and lanes magenta, track buttons violet; the colour returns when the value is set to 0. **All** (Extend) sets the global octave value (not applied to Drums tracks) and skips Drums tracks for scale and root; the tempo divider (Div) is applied to all tracks including drums. Any other button ends the selection and then does its normal job; a pad press that is not an Oct destination just ends it. After **Mode**, Scale◀/▶ open pattern view and Root−/+ start a root selection (Mode ends).

### Sequence length

- Use **Length** (1–16), or press **Mode** and then Pad A top row buttons 3 and 4 (Len−/Len+, shorter and longer; Mode stays on). Without Mode these buttons are Div−/Div+.
- Steps beyond the length are dark on the pads and faded on screen. Their notes are kept and come back when you lengthen the sequence again.
- Changing the length while playing never restarts the sequence.

------------------------------------------------------------------------

## 5. Working with the sequence

### Loops per lane (polymetric patterns)

Each lane can loop over its own range of steps, so lanes of different lengths drift against each other.

1.  Press a button in the **Pad B right column** (one per lane; its label shows the lane's note). It turns white.
2.  Press a pad in that lane for the **start** step.
3.  Press a pad in that lane for the **end** step. The two presses can come in either order.

Pressing the same pad twice gives a one-step loop. Press the lane button again before the second press to cancel and keep the old range. To go back to the full length, set the range from step 1 to step 16.

Lanes with a custom range show dark blue between the start and end, with brighter blue on the first and last step, and their button in the right column is lit blue. The overall length still limits every lane.

### Chromatic scale: sharps and flats

The **Chromatic** scale starts out like C major: the eight lanes are **C D E F G A B C** (they follow the Root setting). Single notes can then be sharpened or flattened.

On a track set to Chromatic, Pad B top row buttons 5 and 6 change their job: instead of Root − / +, they become **♭ (flat)** and **♯ (sharp)** mode buttons (the on-screen labels change too).

1.  Press **♯** (button 6). It flashes red. Now press a note on the grid to **sharpen** it. Press an **empty pad** to create a sharpened note.
2.  Press **♭** (button 5). It flashes red. Now press a note to **flatten** it, or an empty pad to create a flattened note.
3.  Instead of a note, press a **lane button** in the Pad B right column to apply the accidental to the **whole lane**: every note in that lane of the selected track is sharpened (or flattened). Lanes that cannot take the accidental (E and B for sharp, F and C for flat) are shown dim and ignored. Empty steps stay empty.
4.  Press the same button again to leave the mode. Starting another mode also ends it.

Rules:

- **E and B cannot be sharpened** and **F and C cannot be flattened**. These presses are ignored (an empty pad stays empty). The rule follows the lane's place in the scale, so it still applies after you change the Root.
- A note has at most one accidental. Pressing a sharpened note in flat mode (or a flattened note in sharp mode) brings it back to natural. Pressing ♯ on a sharpened note changes nothing.
- **Deleting a note** (pressing it in normal mode) also removes its sharp or flat.
- Sharp notes are drawn lighter than natural notes and flat notes darker. On screen they also carry a ♯ or ♭ sign.
- Held notes keep their accidental, also when you extend them backwards.
- The accidentals are stored in the track but only sound while the track is on Chromatic. If you switch the track to another scale, they are ignored, and they come back when you switch to Chromatic again. Clearing or randomizing a track removes them.
- The lane buttons of Pad B keep showing the natural note names. A lane press follows the same one-step rule as a note press, so pressing ♯ on a lane whose notes are already sharp changes nothing, and pressing ♭ brings them back to natural.

### Held notes (extend note)

A note can last several steps. It is played as one held note.

1.  Press the **Extend** button (top row button 8 on either pad). It flashes red and stays on until you press it again, so you can extend several notes in a row.
2.  Press an existing note. It turns white.
3.  Press a step in the **same lane**, before or after the note.

The note now covers all the steps in between. The extra steps are drawn in a dimmer version of the track colour. To shorten it, press one of the covered steps (the note ends just before it). Press the first step to remove the note. A held note is clipped at the end of its lane's loop.

### Velocity, gate length and probability (per step)

Press **Vel**, **Gate** or **Prob** in the Pad A right column (the three buttons below T4: Prob, Gate, Vel from the top) to open a view in which each column is a **step** and the height sets the value for **all notes in that step**. Press the same button again to go back.

- **Velocity** (default 100): on the pads there are 8 levels from 16 to 127. Sliders on screen give the exact value from 1 to 127.
- **Gate length** (default 50%): the share of the step a note lasts, from 13% to 100% in 8 levels on the pads, or exactly 1–100% with the on-screen sliders. At 100% a note is held for the whole step. Held (extended) notes ignore the gate.
- **Probability** (default 100%): the chance that the step is played, 0 = never and 100 = always. On the pads there are 8 levels from 13% to 100%. Press the **bottom pad of a column twice** to set 0%. The on-screen sliders give the exact value from 0 to 100. A step is decided as a whole: all of its notes play together or are skipped together, and the roll is made again every time the step comes round.

**Setting all steps at once:** in these views the **Pad B right column becomes a master slider**. Pressing one of its eight buttons sets **all 16 steps** of the selected track to that level (top button = highest, bottom = lowest; the buttons are labelled with the value they set). The lit buttons show the average of the steps. In the probability view press the bottom button twice to set 0%. On screen the master slider is one vertical slider beside Pad B.

**For one lane only:** press a **Pad B lane button** (it turns white), then **Vel**, **Gate** or **Prob**. The view then edits that lane's values only (the status line says which lane), and the master slider sets all 16 steps of that lane. The lane starts from the track's values and keeps its own from the first edit. In probability, a lane with its own values makes its own roll for each step. To return to the track values, open the view without choosing a lane (Press the view button to close it first) and move the master slider (Pad B right column) or press one of its buttons: this resets that parameter for **all** lanes. Editing single steps of the track view does not affect lanes that have their own values.

The views show the playhead. On screen you can **drag across several sliders**, also from one pad to the other, to draw a curve. A fast swipe fills the sliders it skips. Sliders also work with the arrow keys.

### Stepping modes

A track can play its steps forward, backward and in other orders.

1.  Press **Mode** in the Pad A right column (the **bottom** button).
2.  The five buttons from T1 down to **Prob** show the modes. Press one:
    - **T1** forward (default)
    - **T2** reverse
    - **T3** ping-pong
    - **T4** random
    - **Prob** random walk
3.  The mode applies to the whole track. Mode stays on (flashing red) so you can choose again; press **Mode** again to leave. A lane selected with its lane button stays selected (white) until you press it again. **Gate** switches Jump on or off (see below) and **Vel** cycles [Key mode](#key-mode).

To give **one lane** its own mode, press **Mode**, press a lane button in the Pad B right column, then press a mode button. That lane now follows its own mode. Choosing a mode for the whole track clears all the lane settings of that track.

How the modes behave:

- Forward, reverse and ping-pong run every lane inside its own loop range.
- **Random** and **random walk** move **one playhead for the whole track**. All lanes play that step if it lies inside their loop range, so the range works as a filter. Random walk moves one step up or down at random and wraps around.
- A lane with its own random mode moves its own playhead.

**Jump:** press **Mode**, then **Gate** to switch Jump on (or off again) for the selected track. While Jump is on, the bottom row of the note grid glows cyan and the track’s current stepping mode keeps running. **Hold** a bottom-row pad to hold the track’s playhead on that step: the step is played again on every tick for as long as the pad is down. When you let go, the track carries on in its stepping mode from that step. If you hold several pads, the last one pressed counts. A lane whose loop range does not contain that step ignores the hold and runs on as before. While Jump is on, the bottom row no longer toggles notes (switch Jump off to edit them). Jump needs the sequencer to be playing, and each track has its own Jump switch. With Jump on, **Extend** (labelled All, red while active) toggles **Jump all**: a held bottom-row pad then holds the playhead of all four tracks on that step. Jump all ends when Jump is switched off.

**Playhead reset:** press **Mode**, then **Mute** (labelled Reset) to cue a reset for the **selected track**. To cue **all tracks**, press **Mode**, then **Extend** (labelled All, flashes red), then **Mute**: when the selected track next loops, every lane of every track jumps to step 1. Press again to cancel. When a track next loops to step 1 (every Length steps of its own speed), every lane jumps to step 1, including lanes with their own loop range. The track then carries on in its stepping mode; a lane whose loop range does not start at step 1 runs freely along the track until it reaches its range, and from then on loops within that range again. In the random modes the shared playhead restarts at step 1 and lanes outside their range stay silent as usual. The cue is not saved; while it is pending, Mute flashes white when Mode is on.

### Patterns

Every track has **8 patterns**. A pattern holds everything of the track (notes, velocity, gate, probability, mutes, loop ranges, stepping modes, Jump, scale, root shift, octaves, tempo divider) except the MIDI output and channel. Pattern 1 is the default.

1.  Press **Mode**, then **Scale◀** or **Scale▶** (Pad B top row, they show Pat) to open **pattern view**.
2.  On Pad A the top four grid rows are tracks 1–4 (the same rows as the track buttons), patterns 1–8 from the left. The row below switches **all four tracks** at once.
3.  Press a pattern to **cue** it: its LED flashes and it starts when the track's playhead returns to step 1 (every Length steps of that track's own speed). Press it again to switch **immediately**. Pressing the active pattern cancels a cue. While stopped, patterns switch at once.
4.  To **copy**: press **Vel** (labelled Copy), then the destination pattern. The active pattern of that track (or of every track, in the bottom row) is copied there.

Bright = active, flashing = cued, dim = contains notes. T1–T4 and Play work in pattern view; Mode leaves it (T1–T4 select the track). Patterns are saved with the session.

### Drum maps

A drum map defines what each of the 8 lanes of a track on the **Drums** scale plays. The built-in map is the General MIDI map in [9.5](#95-drum-map-drums-scale-bottom-lane-to-top-lane). To use your own, write a JSON file and click **Load** next to *Drum map* in the MIDI devices section. **Default** returns to the built-in map.

```json
{
  "name": "My kit",
  "lanes": [
    { "lane": 1, "label": "BD", "note": 36, "channel": null },
    { "lane": 2, "label": "SD", "note": 38, "channel": 10 }
  ]
}
```

- **lane**: 1 (bottom) to 8 (top).
- **label**: up to two characters, shown on the Pad B lane buttons (on screen) while a Drums-scale track is selected. Without a map those buttons show the note name.
- **note**: MIDI note 0–127.
- **channel**: MIDI channel 1–16. Leave it empty (`null`, `""` or omit it) to use the track's own channel.
- Lanes missing from the file keep the default. A file with an error is rejected and the status line says why.

The map applies to **every track set to Drums**, to the sequencer and to the Key mode drum pads (including their channels). Octave offsets still apply to the mapped notes. The map is saved with the MIDI settings (**Save All** and **Save MIDI**); the complete map is stored in the session file, so the original map file is not needed later. An example with the default map is `drum-map-default.json`.

------------------------------------------------------------------------

## 6. Performance tools

### Mute mode

Press **Mute** (Pad A top row, second button). It flashes red and stays on until you press it again. Then press a target:

| Target                                        | Effect                                                        |
|-----------------------------------------------|---------------------------------------------------------------|
| A **lane** button (Pad B right column)        | Mutes or unmutes that lane of the selected track              |
| A **track** button T1–T4 (Pad A right column) | Mutes or unmutes that track                                   |
| A **bottom-row pad** (either pad)             | Mutes or unmutes all notes in that step on the selected track |
| The **Extend** button | Mutes or unmutes the whole app (the sequence keeps running)   |

Muted things show red, active ones green. Muted notes are dimmed on the grid.

### Clear mode

Press **Mode**, then **Oct−** (Pad B top row, button 1; labelled CLR while Mode is on) to switch clear mode on. Oct−, the track buttons, the lane buttons, Extend and the bottom row of the note grid flash red. Press one target and the mode ends. Press **Oct−** again to cancel clear mode; any other button cancels it and then does its normal job:

| Target                                 | Effect                                             |
|----------------------------------------|----------------------------------------------------|
| A **track** button T1–T4               | Clears that track                                  |
| A **lane** button (Pad B right column) | Clears that lane of the selected track             |
| A **bottom-row pad**                   | Clears that step (all lanes) of the selected track |
| The **Extend** button (labelled All) | Clears every track |

Clear mode removes **notes only**. Velocity, gate, probability, mutes and loop ranges stay. A held note that covers a cleared step is shortened. Other pad presses are ignored while clear mode is on.

### Key mode

Key mode turns both pads into the same keyboards, so you can play the selected track's sound by hand. Press **Mode**, then **Vel** (labelled **Key**). Keep pressing **Vel** while Mode is on to cycle (see below); press **Mode** again to switch Mode off.

- Only the selected track is affected: its sequencer is switched off (its settings are kept) while all other tracks keep playing. Play / Stop still works. The mode belongs to the track and is saved with the session: each track is either in Key mode or in sequencer mode, and choosing a track with T1–T4 switches the pads to that track's mode.
- Each pad shows **four keyboards of two rows** covering four octaves from the bottom up: white keys in the lower row (C D E F G A B C), black keys in the upper row above D, E, G, A and B (C♯, D♯, F♯, G♯, A♯, each one column to the right of its natural note; only on the Chromatic scale). The **second keyboard from the bottom** is the track's octave and is drawn in the track colour. Root keys are green (bright track colour in the current octave).
- On a **diatonic scale** (or any scale except Chromatic) each keyboard is **one row of scale notes starting with the root at the left**; the upper row stays dark. On the **Drums** scale each pad becomes eight large drum pads, each four note pads wide and two high, in their own colours: lowest drum at the bottom left, highest at the top right (Pad A and Pad B show the same pads). A Pad A drum pad sounds while held. A Pad B drum pad is **retriggered in time with the track's step speed** (tempo and Tempo ÷) for as long as it is held; while the sequencer plays, the hits fall on its steps, otherwise they follow the BPM. The Oct buttons shift the drum notes. On the Chromatic scale the piano layout follows the Root.
- **Pad A**: a note sounds as long as you hold the key. **Pad B**: keys are **latched**; press once to start a note, again to stop it. **Velocity** is set with the Pad B right column, which becomes a slider with the same 8 levels as the master velocity slider (16 to 127; default 100, each track keeps its own value). On the Drums scale Pad B does not latch (see above).
- **Cycling:** while Mode is on, each press of **Vel** (labelled with the next state: **Keys**, **Drum** or **Seq**) steps through keyboard, drum pads and back to the sequencer. On the Drums scale the first press gives drum pads and the second a piano keyboard; on every other scale the first press gives the keyboard and the second drum pads that play the scale's notes from the root (the eight lanes: C D E F G A B C on Chromatic), lowest at the bottom left. Only on the Drums scale do the pads use General MIDI drum notes. While Mode is off, **Vel** does nothing in Key mode (velocity is the Pad B right column slider); in sequencer mode it opens the velocity view again.
- The **top row of the grid** shows where the track's silent sequencer is: the playhead moves along it as a **red LED** (steps 1–8 on Pad A, 9–16 on Pad B) while the sequencer is playing.
- Notes go to the track's channel and instrument output. **Mute** works as usual: press Mute, then T1–T4 to mute or unmute tracks (a muted track's keyboard is silent too). **Oct−** and **Oct+** change the track octave directly. **Scale** and **Root** buttons (Pad B top row) and the on-screen dropdowns work as usual. Notes stop when the track, scale, root, octave, channel or output changes, and when you leave Key mode. Other buttons are dark and do nothing.

### Randomize

**RND** replaces the selected track with a random pattern (fewer notes on melodic tracks, a little more on drums).

### Swap pads

Press **top row button 7** on either pad to swap their roles. The pad that showed steps 1–8 then shows steps 9–16, and the other way round, together with their controls. Press it again to swap back.

### Character display

While an **Oct, Scale or Root selection** is active (see [Selection buttons](#selection-buttons-oct-scale-root)), Pad B's note grid shows the selected value as two 3×5 dot characters, starting one row above the bottom row at the left, with one empty column between them. The normal view is hidden meanwhile, but the track keeps playing. The display disappears after 2 seconds (or when the selection is applied or cancelled); the selection stays active, its buttons keep flashing, and pressing Oct, Scale or Root again shows it again.

- **Octave:** sign (+, − or blank) and digit, for example +2.
- **Scales:** P- minor pentatonic, P+ major pentatonic, IO Ionian, DO Dorian, PH Phrygian, LD Lydian, MX Mixolydian, AE Aeolian, LO Locrian, CH Chromatic, Dr Drums.
- **Root:** the note letter, with # for a sharp (for example C, D#).
- **Tempo:** pressing BPM− or BPM+ shows the BPM for 2 seconds (restarted by every change): the hundreds digit right-aligned on Pad A, tens and ones on Pad B (for example 98 = blank, 9, 8). With external clock it shows the measured tempo.
- **Tempo divider:** the selected note value (for example 32T, 16D, 8, *1) for 2 seconds, right-aligned over both pads: the first character on Pad A (right three columns), the other two on Pad B.
- On screen the display appears on Pad B's note grid (not in the slider views).

------------------------------------------------------------------------

## 7. Saving and sync

### Saving

- **Automatic:** all settings, tracks and sequences are saved in the browser a moment after every change and restored when the page opens. If you were connected to MIDI, it reconnects.
- **To a file:** **Save All** downloads MIDI settings and patterns (`twin-pad-all.json`), **Save MIDI** only the MIDI settings (`twin-pad-midi.json`: devices, Launchpad models, clock input, external clock, the drum map, LED brightness, and each track's output and channel) and **Save Ptn** only the patterns (`twin-pad-patterns.json`: everything else, including tempo, length and root). One **Load** button loads any of these files, and only the settings found in the file are changed. Use this for backups and for moving between computers, because the automatic copy belongs to one browser and one way of opening the page.
- **Not saved:** whether it is playing, the current mode or view, and the whole-app mute.
- **Reset:** the **RESET** button (bottom right of the page) deletes the autosaved data and all settings after a confirmation.

### External clock and start / stop

- **Start and stop** messages from the **Clock input** device always control the sequencer: Start restarts from step 1, Continue resumes, Stop stops.
- Click the **External clock** button to make the tempo follow the MIDI clock from that device. The BPM field is locked and shows the measured tempo. Switching external clock on or off stops playback. If no clock input is selected the status line asks you to pick one.
- With external clock on, pressing Play waits for the next clock tick.

------------------------------------------------------------------------

## 8. Troubleshooting

| Problem                                                       | What to check                                                                                                                                                                        |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| No MIDI inputs and outputs listed                             | Use a Chromium based Browser (Chrome or Edge). Allow MIDI and SysEx when asked. Reconnect the Launchpads and click **Connect MIDI** again.                                           |
| Pads stay dark or behave oddly                                | Use the **MIDI** port, not the **DAW** port (automatic setup avoids DAW ports, but a port you chose by hand is used as is). Check the **model** dropdown.                            |
| The grid works on screen but not on the pads (or the reverse) | The input and output of a pad are separate selectors. Set both.                                                                                                                      |
| Notes play on screen but you hear nothing                     | Pick an **Instrument output** for that track (it is set per track). Check the track's **Channel**, the whole-app mute (red Extend button), and that the synth listens on that channel. |
| Steps 9–16 do nothing                                         | The **Length** may be shorter than 16, or the pads may be swapped.                                                                                                                   |
| A lane stays silent                                           | It may be muted (Mute mode shows red), or in a random mode its loop range may not contain the playhead.                                                                              |
| Bottom-row pads do not toggle notes                           | Jump is on for the track (cyan bottom row). Press **Mode**, then **Gate** to switch it off.                                                                                          |
| A track plays slower than the others                          | Its **Tempo ÷** is slower than 16.                                                                                                                                                          |
| The pads show keyboards, the track is silent                  | Key mode is on for that track. Press **Mode**, then **Vel** until the label shows the sequencer again.                                                                                                             |
| Tempo does not change                                         | **External clock** is on and locks the BPM field; the BPM buttons then only show the measured tempo.                                                                                                                                    |
| Settings are gone after reopening                             | The page was opened differently (disk versus `localhost`) or the browser data was cleared. Use **Save** for backups.                                                                 |
| Colours look different on a Launchpad                         | The original Launchpad (X-Y and Drum Mode) has only red and green. The Pro uses a lower colour resolution.                                                                           |

------------------------------------------------------------------------

## 9. Reference

### 9.1 Defaults and limits

| Setting              | Default                                         | Range                   |
|----------------------|-------------------------------------------------|-------------------------|
| Tempo                | 120 BPM                                         | 30–300                  |
| Sequence length      | 16 steps                                        | 1–16                    |
| Velocity per step    | 100                                             | 1–127                   |
| Gate length per step | 50%                                             | 1–100%                  |
| Probability per step | 100%                                            | 0–100%                  |
| Octave per track     | 0                    | −3 to +3                |
| Channel              | Track 1: 1, track 2: 2, track 3: 3, track 4: 10 | 1–16                    |
| Tempo divider        | 16 (sixteenth notes)                            | 32T … *2 (20 values)    |
| Instrument output    | First port that is not a Launchpad (per track)  | any MIDI output or none |
| Scale                | Minor pentatonic (track 4: Drums)               | see [9.4](#94-scales)   |
| Root                 | C                                               | C to B                  |
| Stepping mode        | Forward                                         | 5 modes                 |
| Jump                 | Off                                             | on / off per track      |
| Launchpad model      | LP Mini MK3                                     | 5 choices               |

### 9.2 Hardware buttons

Numbers count from the left (top rows) or from the top (right columns). Labels in brackets are the on-screen labels.

**Pad A top row**

| # | Button | Function |
|---|---|---|
| 1 | Play / Stop | Start or stop. In clear mode: cancels it. |
| 2 | Mute | Toggle mute mode. After Mode (labelled Reset): cue a playhead reset. |
| 3 | Div− (Len− after Mode) | Select the next faster tempo division (shown on both pads); then press a track button or Extend (all tracks). After Mode: shorten the sequence by one step. |
| 4 | Div+ (Len+ after Mode) | Select the next slower tempo division; then press a track button or Extend (all tracks). After Mode: lengthen the sequence by one step. |
| 5 | BPM− | Tempo −1 (hold over 1.5 s: −10 every 0.5 s); shows the tempo. After Mode (labelled Opt): options view (LED brightness). |
| 6 | BPM+ | Tempo +1 (hold over 1.5 s: +10 every 0.5 s); shows the tempo. After Mode (labelled Opt): options view (LED brightness). |
| 7 | Swap | Swap the two pads |
| 8 | Extend | Extend a note. In mute mode: whole-app mute. During an Oct, Scale, Root or Div selection: apply to all tracks. After Mode (All): arm a reset of all tracks, then press Mute. With Jump on: toggle Jump all. |

**Pad B top row**

| # | Button | Function |
|---|---|---|
| 1 | Oct− | Octave selection −1 (shown on Pad B); then press a note, empty pad (step), lane, track or Extend (all). After Mode (labelled CLR): clear mode. |
| 2 | Oct+ | Octave selection +1; then choose the destination as for Oct− |
| 3 | Scale◀ | Select the previous scale; then press a track button or Extend (all). After Mode: pattern view. |
| 4 | Scale▶ | Select the next scale; then press a track button or Extend (all). After Mode: pattern view. |
| 5 | Root− (♭ on Chromatic) | Select root −1 semitone; then press a track button or Extend (all). On a Chromatic track: toggle flat mode (after Mode: root selection). |
| 6 | Root+ (♯ on Chromatic) | Select root +1 semitone; then press a track button or Extend (all). On a Chromatic track: toggle sharp mode (after Mode: root selection). |
| 7 | Swap | Swap the two pads |
| 8 | Extend | Extend a note. In mute mode: whole-app mute. During an Oct, Scale, Root or Div selection: apply to all tracks. After Mode (All): arm a reset of all tracks, then press Mute. With Jump on: toggle Jump all. |

**Pad A right column**

| # | Label | Normal function | During stepping selection |
|---|---|---|---|
| 1 | T1 | Select track 1 | Forward |
| 2 | T2 | Select track 2 | Reverse |
| 3 | T3 | Select track 3 | Ping-pong |
| 4 | T4 | Select track 4 | Random |
| 5 | Prob | Open or close the probability view | Random walk |
| 6 | Gate | Open or close the gate view | Jump on / off |
| 7 | Vel | Open or close the velocity view. Idle in Key mode. | Cycle keyboard / drum pads / sequencer |
| 8 | Mode | Start stepping mode selection | Cancel |

In mute mode the track buttons mute tracks, in clear mode they clear tracks, and during an Oct, Scale or Root selection they are the track destinations.

**Pad B right column:** one button per lane, lane 8 at the top and lane 1 at the bottom. On screen each button is labelled with the note name of its lane for the selected track (for example C, Eb, F#, Bb, without the octave), and the labels follow the scale, root and octave. On the Drums scale they show the pitch class of the drum note, or the two-letter label when a custom drum map is loaded.

| Situation | Function |
|---|---|
| Normal | Set the lane's loop start and end (see [Loops per lane](#loops-per-lane-polymetric-patterns)) |
| Octave selection | Apply the octave to the whole lane |
| Mute mode | Mute or unmute the lane |
| Stepping selection | Select the lane for its own stepping mode |
| Clear mode | Clear the lane |
| Velocity, gate or probability view | Master slider: set all steps to the button's level |
| Sharp or flat mode (Chromatic) | Sharpen or flatten every note of the lane |
| Key mode | Velocity slider for the keyboard: same 8 levels as the master velocity slider (16 to 127), default 100 |

**Note grid (both pads):** normally toggles notes. In the velocity, gate and probability views it sets the step's value. With Jump on, a held bottom-row pad holds the track on that step. In the options view the bottom row of Pad A is the LED brightness slider (the rest is dark). In mute and clear mode the bottom row selects a step, and the other rows are ignored in clear mode. In Key mode both grids are keyboards (see [Key mode](#key-mode)).

### 9.3 On-screen controls

| Control                                          | Function                                                                                            |
|--------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Connect MIDI                                     | Ask for MIDI access                                                                                 |
| Pad A / B input, output, model                   | Devices and Launchpad models                                                                        |
| Clock input                                      | Source of MIDI clock and start / stop                                                               |
| Drum map: Load / Default | Load a custom drum map file, or return to the default map |
| BPM                                              | Tempo. Locked and showing the measured tempo with external clock.                                   |
| External clock                                   | Follow incoming MIDI clock                                                                          |
| Length                                           | Sequence length                                                                                     |
| Save All / Save MIDI / Save Ptn / Load           | Session files: both, MIDI settings only, patterns only; Load reads whichever fields a file contains |
| Scale                                            | Scale of the selected track                                                                         |
| Root                                             | Root note (shows the selected track's; moves all tracks together)                                   |
| Octave                                           | Octave of the selected track (dropdown)                                                             |
| Tempo ÷                                          | Tempo divider of the selected track                                                                 |
| Instrument output                                | MIDI output of the selected track                                                                   |
| Oct− / Oct+ | Select the octave change, then a note, step, lane, track or All |
| Channel                                          | MIDI channel of the selected track                                                                  |
| RND                                              | Randomize the selected track                                                                        |
| On-screen top rows, logo cells and right columns | Same as the hardware                                                                                |
| Velocity, gate and probability sliders           | Exact values, drawable across steps                                                                 |
| Master slider (beside Pad B in the slider views) | Sets all steps of the selected track to one value                                                   |

### 9.4 Scales

Intervals are semitones above the root. Eight lanes run from the bottom up and continue in the next octave when a scale has fewer than 8 notes. Sessions saved with an older version of the Chromatic scale (one lane per semitone) now play the new C-major-based lanes.

| Scale                   | Intervals                                                                                                                    |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------|
| Minor pentatonic        | 0 3 5 7 10                                                                                                                   |
| Major pentatonic        | 0 2 4 7 9                                                                                                                    |
| Ionian (major)          | 0 2 4 5 7 9 11                                                                                                               |
| Dorian                  | 0 2 3 5 7 9 10                                                                                                               |
| Phrygian                | 0 1 3 5 7 8 10                                                                                                               |
| Lydian                  | 0 2 4 6 7 9 11                                                                                                               |
| Mixolydian              | 0 2 4 5 7 9 10                                                                                                               |
| Aeolian (natural minor) | 0 2 3 5 7 8 10                                                                                                               |
| Locrian                 | 0 1 3 5 6 8 10                                                                                                               |
| Chromatic               | 0 2 4 5 7 9 11 (like Ionian; single notes can be sharpened or flattened, see [section 5](#chromatic-scale-sharps-and-flats)) |
| Drums                   | see [9.5](#95-drum-map-drums-scale-bottom-lane-to-top-lane)                                                                  |

The lowest lane is note 48 (C3) plus the root and 12 per octave, and results are limited to 0–127. In every scale except Drums, lanes on a scale degree 0 are the root lanes.

### 9.5 Drum map (Drums scale, bottom lane to top lane)

| Lane | Note | Sound         |
|------|------|---------------|
| 1    | 36   | Bass drum     |
| 2    | 38   | Snare         |
| 3    | 42   | Closed hi-hat |
| 4    | 46   | Open hi-hat   |
| 5    | 39   | Hand clap     |
| 6    | 41   | Low floor tom |
| 7    | 47   | Low-mid tom   |
| 8    | 49   | Crash cymbal  |

### 9.6 Stepping modes

| Mode        | Order                                                                                      |
|-------------|--------------------------------------------------------------------------------------------|
| Forward     | Low to high step, then back to the start of the range                                      |
| Reverse     | High to low step, then back to the end of the range                                        |
| Ping-pong   | Up and down, without repeating the end steps                                               |
| Random      | One random step per tick for the whole track, over the full length                         |
| Random walk | One step up or down per tick for the whole track, wrapping at the ends, starting at step 1 |

Forward, reverse and ping-pong run per lane, within the lane's loop range and the overall length. In the two random modes a lane plays only when the track's step lies inside its loop range. A lane with its own mode always follows that mode.

Jump is a per-track switch on top of the stepping mode. A held bottom-row pad keeps the track on that step; lanes whose loop range does not contain it ignore it, and the mode continues from the new position.

### 9.7 Timing and MIDI output

- **Resolution:** one step is a 16th note. At tempo *T* a step lasts 15000 / *T* milliseconds.
- **Tempo divider:** one step of a track lasts the note value of its divider: *u* × 1250 / *T* milliseconds, with *u* = 4 (32T), 6 (32), 8 (16T), 9 (32D), 12 (16), 16 (8T), 18 (16D), 24 (8), 32 (4T), 36 (8D), 48 (4), 64 (2T), 72 (4D), 96 (2), 128 (*1T), 144 (2D), 192 (*1), 256 (*2T), 288 (*1D), 384 (*2). Gate length and held notes use that step. All tracks fire on one common grid counted from the start of the sequence.
- **Output:** every track sends to its own instrument output.
- **Scheduling:** notes are scheduled ahead with Web MIDI timestamps for steady timing. The scheduler runs in a Web Worker timer, so playback continues at full accuracy when the tab loses focus or the browser is minimized (the lookahead grows from 120 ms to 500 ms while hidden, and the Launchpad LEDs keep updating). Only the on-screen display pauses.
- **Note on:** channel = the track's channel, note = lane pitch, velocity = the step's velocity.
- **Note off:** single-step notes end at the gate share of the step, but at least 1 ms before the next step. Held notes end 0.1 step before the end of their last step.
- **Whole-app mute and stop:** send all-notes-off (CC 123) on the channels in use.
- **External clock:** 24 pulses per quarter note, 6 pulses per step. The tempo shown is measured from the pulses.

### 9.8 LED colours

LED colours are the same on screen and on every Launchpad (reduced to the pad's capabilities).

**Note grid**

| Look | Meaning |
|---|---|
| Track colour (amber, cyan, magenta, lime) | A note |
| White | A note under the playhead, or the selected note in extend mode |
| Dim grey | Playhead on an empty step |
| Dimmer track colour | A held-note continuation, or a muted lane or step |
| Magenta | A note with a per-note octave override |
| Lighter or darker track colour | A sharpened or flattened note (Chromatic scale) |
| Very dark green | A root lane |
| Very dark blue | Inside a custom lane loop range |
| Brighter blue | First and last step of a custom lane loop range |
| Dark or faded | Beyond the sequence length |
| Red, flashing (bottom row) | Clear-step targets (clear mode) |
| Cyan, flashing (bottom row) | Jump targets (Jump on) |
| White and grey bar (bottom row of Pad A) | Options view: LED brightness slider, brightest pad at the value |
| Red or green, flashing (bottom row) | Muted or active steps in mute mode |
| Dim blue-grey, dark (Key mode) | White keys and black keys; the current octave in the track colour; root keys green; a sounding key white |
| Red, top grid row (Key mode) | Playhead of the track's sequencer |

**Top rows**

| Button | Colours |
|---|---|
| Play | Dim green (stopped), green (playing) |
| Mute | Dim yellow, flashing red when mute mode is on |
| Div−, Div+ (Pad A), Oct−, Oct+ (Pad B top row 1–2) | Blue. Oct−/Oct+ flash red while an octave selection is active; Oct− also flashes red in clear mode |
| BPM−, BPM+ (Pad A top row 5–6) | Teal. Colours stay with the button positions, not the functions |
| Scale◀, Scale▶ | Amber |
| Root−, Root+ | Purple. On a Chromatic track: ♭ is dim blue (bright blue in flat mode), ♯ is dim orange (bright orange in sharp mode). |
| Swap | Dim orange, bright when swapped |
| Extend | Dim teal, flashing red when armed. Flashes in mute mode and during a selection (All); red when the whole app is muted; flashing red in clear mode |
| BPM−, BPM+ (Pad A), Scale◀, Scale▶, Root−, Root+ (Pad B) | Keep their colour and flash while Mode is on |
| Logo | Dim red (Pad A role), dim green (Pad B role) |

**Right columns**

| Button | Colours |
|---|---|
| T1–T4 | Track colour, bright when selected. Red for muted tracks in mute mode, flashing red in clear mode. |
| Toggle buttons | Mute, Mode, Extend, ♭, ♯, the active Oct, Scale or Root selection and the open Vel, Gate or Prob view flash red while active. Second-function buttons (T1–T4, Prob, Gate, Vel, lane buttons) keep their colour and flash. |
| Mode | Dim yellow-green, flashing red while Mode is on |
| Prob | Dim green; flashing red when the view is open |
| Gate | Dim blue; flashing red when the view is open |
| Vel | Dim orange; flashing red when the view is open |
| Pad B lanes | Dim blue; blue for a custom loop; white while setting a loop or for the lane chosen in Mode; green or red in mute mode (flashing); flashing red in clear mode |

### 9.9 Launchpad protocols

Numbers are the layout used internally: grid 11–88, top row 91–98, right column 19–89, logo 99.

| Model            | Startup                                       | LED messages                                                    | Input                                                                                                                                                                                                                 |
|------------------|-----------------------------------------------|-----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LP Mini MK3      | SysEx ID 0D: Programmer mode                  | One RGB SysEx, 0–127 per colour                                 | Grid notes, edge buttons as CC or note                                                                                                                                                                                |
| LP X             | SysEx ID 0C: Programmer mode                  | Same as the Mini MK3                                            | Same as the Mini MK3                                                                                                                                                                                                  |
| LP Pro / Pro MK2 | SysEx ID 10: Programmer layout                | RGB SysEx, 0–63 per colour, 40 LEDs per message                 | Same numbering                                                                                                                                                                                                        |
| LP X-Y Mode      | Reset, then layout 1 (`B0 00 00`, `B0 00 01`) | Note On or CC with a red/green velocity code, changed LEDs only | Grid note = row × 16 + column (top row first), right column notes 8, 24 … 120, top row CC 68h–6Fh (104–111)                                                                                                           |
| LP Drum Mode     | Reset, then layout 2 (`B0 00 00`, `B0 00 02`) | Same as X-Y Mode                                                | Drum-rack grid notes 36–99 in four 4×4 blocks (36 bottom left, 52 top left, 68 bottom right, 84 top right; each block counts up from its bottom left), right column notes 100–107 (top to bottom), top row CC 68h–6Fh |

When the page closes, or when you change a pad's model, the pad is returned to its normal mode.

### 9.10 Session file

`twin-pad-all.json`, `twin-pad-midi.json` and `twin-pad-patterns.json` are plain JSON with the same fields, split in two groups. MIDI: `conn`, `sync`, `models`, `devs`, `dmap`, `bri` (LED brightness 1–8) and per track `ch` and `so`. Patterns: everything else. A file contains only the group it was saved for.

| Field                       | Content                                                           |
|-----------------------------|-------------------------------------------------------------------|
| `bpm`, `root`, `sel`, `len`, `oa` | Tempo, root note (0–11), selected track (0–3), sequence length, global octave shift (−3 to +3)    |
| `sync`, `swap`              | External clock and swap state                                     |
| `models`                    | Launchpad models of pad A and B (`mk3`, `x`, `pro`, `xy`, `drum`) |
| `devs`                      | Chosen devices (ID and name per selector)                         |
| `conn`                      | Whether to reconnect MIDI on load                                 |
| `dmap` | Custom drum map (`name` and `lanes` with `lane`, `label`, `note`, `channel`), or null for the default |
| `tracks[]`                  | Four tracks, each with the fields below                           |

Track fields: `scale`, `oct`, `ch`, `mute`, `so` (instrument output: ID and name, or null if never chosen), `du` (tempo division as the step length in twelfths of a 16th note: 4 = 32T … 12 = 16th … 384 = two whole notes; files with the earlier `dv` index or `div` 1–16 still load), `rs` (root shift of this track, 0–11 semitones, added to the global root), `jmp` (Jump on or off), `pi` (active pattern 0–7), `pats` (8 stored patterns, null = empty), `km` (Key mode on or off), `kv` (Key mode velocity, 1–127), `lvel`, `lgate`, `lprob` (per-lane values, lane × 16 + step) with `fvel`, `fgate`, `fprob` (8 flags: the lane uses its own values), `no` (128 per-note octave offsets, lane × 16 + step), `loct` (8 per-lane octave offsets), `dir` (stepping mode 0–4), `ldir` (8 lane overrides, −1 = follow the track), `g` (16 step bitmasks, one bit per lane), `acc` (128 accidentals per note, −1 flat, 0 natural, +1 sharp, lane × 16 + step), `ln` (128 held-note lengths, lane × 16 + step), `vel`, `gate` and `prob` (16 values each), `sm` (16 step mutes), `lm` (8 lane mutes), `lo` and `hi` (8 lane loop starts and ends).

Files from older versions load with defaults for anything that did not exist yet.
