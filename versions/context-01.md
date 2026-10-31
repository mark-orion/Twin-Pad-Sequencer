# Twin Pad Sequencer: current context

Supersedes all earlier context files. Current as of the per-note, per-lane and per-track octave controls in `twin-pad-sequencer-02.html` (2026-10-23).

Current deliverables from the latest change: `twin-pad-sequencer-02.html` (updated app) and `manual-02.md` (matching manual). The original `twin-pad-sequencer-01.html` was not overwritten; the new version was saved as `twin-pad-sequencer-02.html`. This context document is `context-01.md`. Keep the manual in sync with the app.

## What it is
`twin-pad-sequencer.html` is a single self-contained file with vanilla JS and no React. It is a web MIDI step sequencer driven by one or two Launchpads, or by mouse and touch alone, using the Web MIDI API with SysEx. Open it from disk or `localhost` in a Chromium based browser like Chrome or Edge and click **Connect MIDI**.

Working style the user wants: concise answers, small incremental changes, every change presented as the updated file, and `manual.md` updated when behaviour changes. **Nothing has been tested on real hardware or a real touch device.** For the latest octave-control change, the extracted JavaScript passed a `new Function()` syntax check. The new octave workflow has not been verified on real Launchpad/synth hardware. Test scripts in `/tmp/t` (`m.js`, `m2.js`, `pr.js`, `ms.js`, `ch.js`, `cl.js`) mock `navigator.requestMIDIAccess` and may not persist.

## Model
- **Layout:** 4 tracks, each with 8 note lanes (rows) by 16 steps. Pad A is steps 1–8, Pad B is steps 9–16. The overall length is 1–16 and defaults to 16.
- **Per-track data:** `g[16]` (row bitmask), `ln[128]` (held-note lengths), `acc[128]` (accidental per note, −1/0/+1, index lane*16+step), `no[128]` (per-note octave offset, −3..+3, index lane*16+step), `lm[8]` (lane mute), `loct[8]` (per-lane octave offset, −3..+3), `sm[16]` (step mute), `lo/hi[8]` (lane loop range), `vel[16]` (default 100), `gate[16]` (default 50), `prob[16]` (default 100), `dir` (stepping mode 0–4), `ldir[8]` (per-lane override, −1 means inherit), plus `scale`, `oct`, `ch` and `mute`.
- **Global:** `bpm`, `root`, `sel`, `len`, `sync` (external clock), `swap`, `models` (Launchpad model of physical pad A and B), `devs` (device choices) and `conn`.
- **Modes (not persisted):** `vv` (velocity view), `gv` (gate view), `pv` (probability view), `dm` with `dl` (stepping selection), `mm` (mute mode), `mall` (master mute), `ext` (extend note), `arm` (loop edit), `cm` (clear mode), `am` (accidental mode: +1 sharp, −1 flat, 0 off). They cancel each other (each mode button resets the others, `S.am=0` included). `render()` clears `am` when the selected track is not on Chromatic.
- **Scales:** Minor/Major pentatonic, Ionian, Dorian, Phrygian, Lydian, Mixolydian, Aeolian, Locrian, **Chromatic** and Drums. Drum rows map to GM notes 36, 38, 42, 46, 39, 41, 47 and 49.

## Chromatic scale (sharps and flats)
- **Lanes:** Chromatic is `[0,2,4,5,7,9,11]`, so the lanes are C D E F G A B C (like Ionian) and follow the Root. Old sessions that used the former one-lane-per-semitone Chromatic now play these lanes.
- **Buttons:** with the selected track on Chromatic, Pad B top row 5 and 6 are **♭** and **♯** toggles instead of Root −/+ (`accBtn`; `topLbl`/`topTtl` change the on-screen labels; colours: ♭ blue, ♯ orange, bright while the mode is on). On other scales they remain Root −/+.
- **Pads:** in sharp/flat mode `accPress(step,r)` applies `acc + am` to the note covering the pad, or creates the note if the pad is empty. Allowed values are −1..+1, and `canSharp(r)` / `canFlat(r)` ignore E and B (no sharp) and F and C (no flat), derived from the scale degrees, so the rule survives a Root change. Octave changes use a separate armed workflow described below.
- **Lane buttons:** in the mode, a Pad B right-column lane button runs `accLane(r)`: every existing note of that lane gets the same one-step shift under the same rules, and no notes are created. The lane buttons show which lanes accept it (bright orange or blue, dim otherwise).
- **Playback and storage:** the pitch is shifted by `acc` only while the track is on Chromatic; stored accidentals are ignored on other scales. Deleting a note, `wipe`, `clearStep` and RND reset accidentals, and `extPress` carries the accidental to the new start step.
- **Display:** sharp notes are lighter, flat notes darker (`accCol` on the pads, CSS classes `sh`/`fl` plus a ♯/♭ glyph on screen).

## Launchpad models
Dropdowns **Pad A model** and **Pad B model** (`#am`, `#bm`) belong to the physical pad slots and are saved in `S.models`. Default is LP Mini MK3. All internal numbers are "programmer layout" numbers: grid 11–88, top row 91–98, right column 19–89, logo 99.

| Model | Enter / exit | LED output | Input |
|---|---|---|---|
| LP Mini MK3 (`mk3`) | SysEx ID 0x0D: `0E 01` / `0E 00` | `03` RGB SysEx, 7-bit colours | grid notes, edge buttons as CC or note |
| LP X (`x`) | SysEx ID 0x0C, same messages | same | same |
| LP Pro (`pro`) | Launchpad Pro / Pro MK2, ID 0x10: `2C 03` / `2C 00` | `0B` RGB SysEx, 6-bit colours, 40 LEDs per message | same numbering |
| LP X-Y Mode (`xy`) | `B0 00 00` reset, then `B0 00 01` | Note On / CC, velocity = green*16 + red + 12, 4 levels, blue mixed into green, changed LEDs only | note = row*16 + column, right column notes 8…120, top row CC 68h–6Fh (104–111, all eight) |
| LP Drum Mode (`drum`) | `B0 00 00` reset, then `B0 00 02` | same as X-Y | grid notes 36–99 in four 4×4 blocks (bottom-left 36, top-left 52, bottom-right 68, top-right 84, each counting up from its bottom left), right column notes 100–107 (top to bottom), top row CC 104–111 |

- **Functions:** `padInit`, `padExit`, `padSend(pi,list)`, `origAddr`, `parseIn`. `onPad(i,d)` parses, maps physical to logical pad (swap), then calls `pressPad` or `ctrl`. `led(i)` builds `[[number,r,g,b],…]` and calls `padSend`.
- **Automatic setup:** `isDaw`, `isAnyLP`, `isLP`. Auto-assignment never uses a port whose name contains "DAW" (nor Launchpad "standalone" ports). The default Instrument output is the first port that is not a Launchpad and not a DAW port. The model is guessed from the port name on the first connect only. Ports chosen by hand are used as is.
- **Not supported:** the Launchpad Pro MK3.

## Hardware map (logical pads; `S.swap` exchanges roles)
Colours are the same on every model. Index 99 is the logo (dim red for Pad A, dim green for Pad B).

**Pad A top row:** Play/Stop, Mute mode, Length −, Length +, Octave −, Octave +, Swap, Extend note. Oct−/Oct+ arm a target octave change; then press a note pad, a Pad B lane button, or T1–T4.

**Pad B top row:** Tempo −5, Tempo +5, Scale previous, Scale next, Root − (♭ on Chromatic), Root + (♯ on Chromatic), Swap, Extend note.

**Pad A right column, top to bottom:** T1, T2, T3, T4, **Mode** (stepping modes; it was called "Step" earlier), **Prob**, **Gate**, **Vel**.

**Pad B right column:** one button per lane (lane 8 at the top). On screen it is labelled with the note name of the lane (`LBL`: C, C#, D, Eb, E, F, F#, G, Ab, A, Bb, B).
- Normal use: set a lane's loop start and end (press the button, then two pads).
- Mute mode: mute the lane. Stepping selection: select the lane. Clear mode: clear the lane. Sharp/flat mode: apply the accidental to the whole lane.
- In the velocity, gate and probability views it is a **master slider**: each button sets ALL 16 steps of the selected track to its level (`setAll`), labelled with that value. In the probability view the bottom button twice gives 0%. The LEDs show the average of the steps.

**Workflows:**
- **Mute mode:** it stays on until pressed again. Targets are a lane, a track (Pad A right column), a step (bottom pad row) or the Play button (master mute).
- **Stepping mode:** press Mode (the button below T4), optionally press a lane button first for a per-lane override, then press a mode button once: T1 forward, T2 reverse, T3 ping-pong, T4 random, or Mode again for random walk (it doubles as the fifth mode button, so cancel by pressing Prob, Gate or Vel). A whole-track choice clears lane overrides.
- **Clear mode (on-screen CLR button only):** one-shot, notes only. Targets: a track button, a lane button, a bottom-row note pad (a step), or Play (everything). Functions: `cmBtn`, `wipe`, `clearTrack`, `clearLane`, `clearStep`, `clearAll`.
- **Extend note:** press the button, then a note, then a step in the same lane.
- **Octave targeting:** press **Oct−** or **Oct+** first, then choose the target. A note pad changes that note only; a Pad B lane button changes the entire lane, including empty pads; T1–T4 changes that whole track. Each target change moves by one octave, limited to −3..+3 relative to its parent/default pitch. Note-specific octave overrides are marked magenta; lane buttons are marked magenta when the lane has an octave offset. Switching a note off and on again resets that note’s octave override to default. Track octave is the existing per-track `oct` value; lane and note offsets are added to it. The same offsets are applied to drum-track pitches.
- **Velocity, gate and probability views:** each pad column is a step, 8 levels (velocity 16–127; gate 13–100%; probability 13–100%, bottom pad twice = 0%). Exact values come from the on-screen sliders. One drag can draw across sliders and across pads.

## Playback rules
- **Clock:** internal clock with a 25 ms interval and 120 ms lookahead, using Web MIDI timestamps. External clock at 24 PPQN with 6 ticks per step; the BPM field shows the measured tempo.
- **Start and stop:** Start, Continue and Stop on the clock input always work.
- **Lane position:** each lane keeps its own position (`pos`), so changing the length or a lane range never restarts playback.
- **Stepping modes:** forward, reverse and ping-pong are per lane. Random and random walk use ONE shared playhead per track (`tp`); lanes play only if that step is inside their range. Lanes with an override use their own mode.
- **Probability:** one random roll per track, step and tick (`ok(s)` in `sched`), shared by every lane on that step, so all notes of a step play or are skipped together.
- **Gate length:** a single-step note-off is at `min(d*gate, d−1)`. A held note's note-off is at `d*(L−0.1)`.

## UI as it stands
- **Top bar (sticky):** BPM, an **External clock** toggle, Length, **Save**, **Load**.
- **Settings row:** Scale (spare width), Root, Octave, Channel (56 px each), **CLR**, **RND**.
- **MIDI devices (collapsible, closed on small screens):** two rows of four. Row 1: Pad A input, output, model, Instrument output. Row 2: Pad B input, output, model, Clock input. It uses 2 columns below 900 px and 1 column below 480 px.
- **Pad edges:** each pad shows its top row, logo cell and right column, mirroring the Launchpad LEDs through `topCol` and `rightCol`, and clickable through `ctrl()`. They appear around the note grid and the three slider views.
- **Slider views:** custom pointer sliders (`makeSlider`, `setSl`), a 9-column CSS grid per pad. Pad A's right-column buttons are square and set the row heights; Pad B has invisible square `.spc` cells for the same purpose, with the master slider (`.ms`, `makeMaster`) spanning rows 2–9.
- **Responsive layout:** pads stack on narrow screens, sit side by side on wide screens, and are sized to fit in landscape on phones.

## Persistence
- **Autosave:** localStorage key `twin-pad-sequencer-v1`, debounced 250 ms. It reconnects MIDI on load if you were connected.
- **File:** Save/Load JSON as `twin-pad-session.json`.
- **Compatibility:** older data loads with defaults (missing `prob` becomes 100, missing `acc` becomes 0, unknown models become `mk3`).
- **Not saved:** play state, modes and views, master mute.

## Code map (inside the one `<script>`)
- **MIDI and models:** `MODELS`, `padInit`, `padExit`, `padSend`, `origAddr`, `parseIn`, `attach`, `fill`, `connect`.
- **Input:** `onPad`, `ctrl` (every top-row and right-column press), `pressPad` (mode-aware), `setAll`, `accPress`, `accLane`, and the mode functions (`velBtn`, `gateBtn`, `probBtn`, `dirBtn`, `muteBtn`, `extBtn`, `cmBtn`, `accBtn`, `laneBtn`).
- **Output:** `led`, `topCol`, `rightCol`, `rightLbl`, `topLbl`, `draw`, `render`, `ui`.
- **Playback:** `sched`, `nextStep`, `trackStep`, `play`, `stop`, `onClock`.
- **Persistence:** `ser`, `apply`, `restore`, `persist`. The current session format saves `no[128]` and `loct[8]`; older saved sessions default these to zero.
- **UI building:** `edges`, `mkRb`, `makeSlider`, `makeMaster`, `setSl`.

## Decisions worth remembering
- The Pad A hold-to-mute button was replaced by the velocity view.
- The first-run default of 8 steps was added and then removed. The default is always 16.
- Random modes use a shared track playhead because per-lane random looked like individual notes being picked.
- Obsolete on-screen buttons were removed whenever a pad button covers the function. Lane loop, mute and mode info is now shown only through LED colours.
- Clear mode is one-shot on purpose. Probability is rolled per step, not per note.
- The Mode button sits under T4 at the user's request, which means it also selects random walk during stepping selection.
- Accidentals shift by at most one semitone and the sharp/flat rules follow the scale degree, not the note name.
- Octave controls are armed: press Oct−/Oct+ first, then choose a note, lane, or track. Note and lane offsets are limited to ±3 octaves; note offsets clear when that note is toggled off/on. Note overrides and nonzero lane offsets use magenta indicators. These offsets apply to melodic and drum tracks.
- A bug to remember: a global string replace that appends `S.am=0;` after `S.gv=false;S.pv=false;` also hit `accBtn` itself and made the toggle always switch on. Fixed with a `was` variable. Watch for this when doing bulk replaces.

## Open points and risks
- **Layout never seen:** the Pad B slider height fix (spacer cells), the landscape phone sizing and the 56 px narrow controls were only reasoned about, not viewed.
- **Models:** the X, Pro, X-Y and Drum protocols are from memory of Novation's references and are unverified (Pro device ID and 6-bit RGB, original-Launchpad velocity flags, Drum Rack note layout and right-column notes 100–107, layout-select messages, logo index 99, Pro edge buttons as CC or note, 40 LEDs per Pro message).
- **Original Launchpad:** Legacy Mode (Mini MK3 in legacy mode) was removed. Old sessions with `legacy` load as `xy`. The app now selects the X-Y or Drum layout itself.
- **Hardware checks:** the Launchpad port choice ("MIDI" rather than "DAW"), SysEx size and rate, note-off timing at 100% gate, and the new octave targeting/LED feedback on actual Launchpad hardware and a synthesizer.
- **External clock:** the alignment when Play is pressed without a Start message.
- **Play and scrolling:** Play is only on the Pad A top row on screen, so it scrolls out of view with the pads.
- **Storage:** localStorage on `file://` is separate from `localhost`.

## Workflow notes
- **Edits:** changes are applied with Python scripts that replace exact unique strings and assert that each matched once, so a script stops at the first anchor that does not match and writes nothing. When that happens, `grep` for the real text.
- **Checks:** run `new Function()` on the extracted script for syntax. jsdom is in `/tmp/t`.
- **jsdom tests:** end them with `w.close();process.exit(0)`, or the process hangs on the page's timers.
