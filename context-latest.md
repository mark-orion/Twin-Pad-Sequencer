Current as of v11 (2026-11-08): octave tiers, channel sliders, reset cues, Jump all, background operation (see "Session changes (v11)" at the end; it supersedes the additive octave selection and the Mode, Mute all-track reset described above).

# Twin Pad Sequencer v10: current context

Current as of v10 (2026-11-07, later session): musical tempo divisions, Div−/Div+ buttons, Mode then Len−/Len+, 1.5 s BPM hold (see "Session changes (v10, part 2)" at the end; it supersedes the integer tempo divider `div` and the old Len buttons described above).

Current as of v10 (2026-11-07): BPM buttons show the tempo and step by 1 (see "Session changes (v10)" at the end). Everything below still applies.

Current as of v09 (2026-11-06): Oct/Scale/Root selection buttons (see "Session changes (v09)" at the end; it supersedes the earlier octave arming `S.om`, the instant Scale/Root buttons, Mode+Root track-only root, the held character display and Play as master mute).

Supersedes all earlier context files. Current as of the v06 session (2026-11-02): custom drum maps (see "Session changes (v06)"). Before that, the v05 session (2026-11-01): 8 patterns per track, the Key mode keyboard/drum pad layout toggle, per-lane velocity/gate/probability, and the split Save buttons (All, MIDI, Ptn) with partial Load. Earlier work: Pad A right column rearrangement and Jump (2026-10-30), per-track instrument output and tempo divider.

Current deliverables from the latest change: `twin-pad-sequencer.html` (updated app) the manual section of `index.html`, `manual.md` and the example `drum-map-default.json`. Keep the manual in sync with the app. Earlier versions are kept as numbered files.

## What it is
`twin-pad-sequencer.html` is a single self-contained file with vanilla JS and no React. It is a web MIDI step sequencer driven by one or two Launchpads, or by mouse and touch alone, using the Web MIDI API with SysEx. Open it from disk or `localhost` in a Chromium based browser like Chrome or Edge and click **Connect MIDI**.

Working style the user wants: concise answers, small incremental changes, every change presented as the updated file, and `manual.md` updated when behaviour changes. **Nothing has been tested on real hardware or a real touch device.** For the latest octave-control change, the extracted JavaScript passed a `new Function()` syntax check. The new octave workflow has not been verified on real Launchpad/synth hardware. Test scripts in `/tmp/t` (`m.js`, `m2.js`, `pr.js`, `ms.js`, `ch.js`, `cl.js`) mock `navigator.requestMIDIAccess` and may not persist.

## Model
- **Layout:** 4 tracks, each with 8 note lanes (rows) by 16 steps. Pad A is steps 1–8, Pad B is steps 9–16. The overall length is 1–16 and defaults to 16.
- **Per-track data:** `g[16]` (row bitmask), `ln[128]` (held-note lengths), `acc[128]` (accidental per note, −1/0/+1, index lane*16+step), `no[128]` (per-note octave offset, −3..+3, index lane*16+step), `lm[8]` (lane mute), `loct[8]` (per-lane octave offset, −3..+3), `sm[16]` (step mute), `lo/hi[8]` (lane loop range), `vel[16]` (default 100), `gate[16]` (default 50), `prob[16]` (default 100), `dir` (stepping mode 0–4), `ldir[8]` (per-lane override, −1 means inherit), plus `scale`, `oct`, `ch`, `mute`, **`so`** (instrument output of the track: `{id,name}`; `null` = never chosen, `{id:'',name:''}` = none), **`div`** (tempo divider 1–16, default 1) and `port` (resolved MIDIOutput, runtime only, not saved) and **`jmp`** (Jump switch, saved) and **`rs`** (per-track root shift 0–11, added to global `S.root`; effective root `eff(t)=(S.root+rs)%12`, used by `pitch`, saved).
- **Global:** `bpm`, `root`, `sel`, `len`, `sync` (external clock), `swap`, `models` (Launchpad model of physical pad A and B), `devs` (device choices) and `conn`.
- **Modes (not persisted):** `vv` (velocity view), `gv` (gate view), `pv` (probability view), `dm` with `dl` (stepping selection), `mm` (mute mode), `mall` (master mute), `ext` (extend note), `arm` (loop edit), `cm` (clear mode), `am` (accidental mode: +1 sharp, −1 flat, 0 off). They cancel each other (each mode button resets the others, `S.am=0` included). `render()` clears `am` when the selected track is not on Chromatic.
- **Scales:** Minor/Major pentatonic, Ionian, Dorian, Phrygian, Lydian, Mixolydian, Aeolian, Locrian, **Chromatic** and Drums. Drum rows map to GM notes 36, 38, 42, 46, 39, 41, 47 and 49 unless a custom drum map is loaded (see v06).

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
- **Automatic setup:** `isDaw`, `isAnyLP`, `isLP`. Auto-assignment never uses a port whose name contains "DAW" (nor Launchpad "standalone" ports). Tracks whose `so` is `null` get the first port that is not a Launchpad and not a DAW port as their instrument output (in `fill`). The model is guessed from the port name on the first connect only. Ports chosen by hand are used as is.
- **Not supported:** the Launchpad Pro MK3.

## Hardware map (logical pads; `S.swap` exchanges roles)
Colours are the same on every model. Index 99 is the logo (dim red for Pad A, dim green for Pad B).

**Pad A top row:** Play/Stop, Mute mode, Length −, Length +, Tempo −5, Tempo +5, Swap, Extend note. Oct−/Oct+ arm a target octave change; then press a note pad, a Pad B lane button, or T1–T4.

**Pad B top row:** Octave −, Octave +, Scale previous, Scale next (after Mode: pattern view, labelled Pat), Root − (♭ on Chromatic), Root + (♯ on Chromatic), Swap, Extend note.

**Pad A right column, top to bottom:** T1, T2, T3, T4, **Prob**, **Gate**, **Vel**, **Mode** (stepping modes; the bottom button; it was called "Step" earlier). Prob, Gate and Vel moved up one place when Mode moved to the bottom.

**Pad B right column:** one button per lane (lane 8 at the top). On screen it is labelled with the note name of the lane (`LBL`: C, C#, D, Eb, E, F, F#, G, Ab, A, Bb, B).
- Normal use: set a lane's loop start and end (press the button, then two pads).
- Mute mode: mute the lane. Stepping selection: select the lane. Clear mode: clear the lane. Sharp/flat mode: apply the accidental to the whole lane.
- In the velocity, gate and probability views it is a **master slider**: each button sets ALL 16 steps of the selected track to its level (`setAll`), labelled with that value. In the probability view the bottom button twice gives 0%. The LEDs show the average of the steps.

**Workflows:**
- **Mute mode:** it stays on until pressed again. Targets are a lane, a track (Pad A right column), a step (bottom pad row) or the Play button (master mute).
- **Stepping mode:** press Mode (bottom button), optionally press a lane button first for a per-lane override, then press a button once: T1 forward, T2 reverse, T3 ping-pong, T4 random, **Prob random walk**, **Gate Jump on/off** (track-wide, toggles `t.jmp`, ends the selection). Cancel with Mode again; **Vel** switches Key mode on/off. A whole-track mode choice clears lane overrides.
- **Tempo divider mode (`tdm`, not persisted):** Mode, then BPM− or BPM+ on Pad B (top row 1/2; `ctrl` checks `S.dm`) sets `S.tdm`. The four top note rows of both pads are sliders over 16 columns (row 8 = track 1 … row 5 = track 4; `pressPad` sets `tracks[7-r].div=step+1`). LEDs and on-screen cells (`led`, `draw`) show a bar up to the divider, brightest at the value; the cell shows the number. The BPM buttons turn yellow (labelled Div) during stepping selection and cyan in the mode. Any `ctrl` button press leaves the mode (Mode just leaves); `cmBtn` clears it too. It takes precedence over every pad action in `pressPad`.
- **Jump (hold):** per-track switch `jmp`. While on, a bottom-row pad press (`pressPad` → `jumpTo(step)`, only while playing and step < length; lane-1 loop editing has priority) pushes `{s,used,rel}` onto `holds[track]`, and its release (`releasePad`) marks `rel`. It does not toggle notes. The bottom row glows cyan (LED and `.cell.jmp`). In `sched` the newest hold entry gives the step `jp` for the track on every tick while the pad is held, so the step is replayed each tick; released entries are removed once used (a short tap still plays one tick). After release the stepping mode carries on from that step (`pos`/`tp` hold it). Per-lane modes: only lanes whose loop range contains the step follow it (others ignore it); shared-playhead modes (random, walk) set `tp`, and out-of-range lanes stay silent as usual. Release comes from `parseIn` (`up:true` for Note Off / velocity 0, handled in `onPad`) and from `pointerup/cancel/leave` on screen cells. `play`, `stop`, window blur and switching Jump off clear all holds.
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
- **Tempo divider:** `sched(cnt,…)` is called once per global step. A track with `div` N only advances when `cnt % N === 0`; otherwise `sched` returns the lane positions unchanged (`pos[ti]`), so the playhead stays put. Tracks stay aligned to the global step count, and Continue keeps the count. All tracks share the same clock, so N=2 is half speed and so on.
- **Per-track output:** each track sends to `tr.port`. `resolveOuts()` (called from `attach` and `applyDevs`) finds the port by id, then by name. `allOff(t)` sends CC 123 on the track's channel; Stop, master mute and changing a track's output use it.
- **Gate length:** with `dd = d*div` (the track's step length), a single-step note-off is at `min(dd*gate, dd−1)`. A held note's note-off is at `dd*(L−0.1)`.

## UI as it stands
- **Top bar (sticky):** BPM, an **External clock** toggle, Length, **Save All**, **Save MIDI**, **Save Ptn**, **Load**.
- **Settings row:** Scale (spare width), Root, Octave, **Tempo ÷** (`#dv`, 1–16), **Instrument output (track)** (`#to`, spare width), Channel, **CLR**, **RND**. Octave, Tempo ÷ and Channel are 56 px wide.
- **MIDI devices (collapsible, closed on small screens):** two rows of four. Row 1: Pad A input, output, model, Clock input. Row 2: Pad B input, output, model. There is no global instrument output any more. It uses 2 columns below 900 px and 1 column below 480 px.
- **Pad edges:** each pad shows its top row, logo cell and right column, mirroring the Launchpad LEDs through `topCol` and `rightCol`, and clickable through `ctrl()`. They appear around the note grid and the three slider views.
- **Slider views:** custom pointer sliders (`makeSlider`, `setSl`), a 9-column CSS grid per pad. Pad A's right-column buttons are square and set the row heights; Pad B has invisible square `.spc` cells for the same purpose, with the master slider (`.ms`, `makeMaster`) spanning rows 2–9.
- **Responsive layout:** pads stack on narrow screens, sit side by side on wide screens, and are sized to fit in landscape on phones.

## Persistence
- **Autosave:** localStorage key `twin-pad-sequencer-v1`, debounced 250 ms. It reconnects MIDI on load if you were connected.
- **File:** three save buttons and one Load (see Save and load). `twin-pad-session.json` files from older versions still load.
- **Compatibility:** older data loads with defaults (missing `prob` becomes 100, missing `acc` becomes 0, unknown models become `mk3`).
- **Not saved:** play state, modes and views (including pattern view, copy arming, lane views `S.vl`), cued patterns (`cue`), master mute.

## Code map (inside the one `<script>`)
- **MIDI and models:** `MODELS`, `padInit`, `padExit`, `padSend`, `origAddr`, `parseIn`, `attach`, `fill`, `connect`.
- **Input:** `onPad`, `ctrl` (every top-row and right-column press), `pressPad` (mode-aware), `setAll`, `accPress`, `accLane`, and the mode functions (`velBtn`, `gateBtn`, `probBtn`, `dirBtn`, `muteBtn`, `extBtn`, `cmBtn`, `accBtn`, `laneBtn`).
- **Output:** `led`, `topCol`, `rightCol`, `rightLbl`, `topLbl`, `draw`, `render`, `ui`.
- **Playback:** `sched`, `nextStep`, `trackStep`, `play`, `stop`, `onClock`, `resolveOuts`, `allOff`, `jumpTo`, `jmpTo`.
- **Persistence:** `ser`, `apply`, `restore`, `persist`. The current session format saves `no[128]` and `loct[8]`; older saved sessions default these to zero. It also saves `so`, `div`, `jmp`, `rs`, `km` and `kv` per track. Old files without `so` give every track the former global output (`devs.so`); old files without `div` get 1.
- **UI building:** `edges`, `mkRb`, `makeSlider`, `makeMaster`, `setSl`.

## Decisions worth remembering
- Key mode playhead: the top grid row (row 8) lights red at the step(s) in `headSet()` (`kh` in `led`, `hs` in `draw`), overriding key colours; it needs the sequencer playing.
- **Key mode (`t.km`, per track, saved; `S.km` is an accessor for the selected track's flag):** Mode then Vel (`keyBtn`; button labelled Key) toggles it for the selected track. Selecting another track (T1–T4) switches the pads to that track's mode; keys sounding on the old track are released. The selected track's sequencer is silenced (`sched` skips its output while `tr.km`; other tracks keep running, Play/Stop work), all its settings are kept, and both note grids turn both note grids into identical keyboards (`kbNote(t,r,c)`, `kbCol`, `led`, `draw`). Per pad: 4 keyboards x 2 rows, the second from the bottom is the track octave (track colour). Chromatic: piano layout (white keys lower row C D E F G A B C, black keys upper row above D E G A B, i.e. one column right of their natural, `KBK=[-1,1,3,-1,6,8,10,-1]`: an assumption about the Mini's layout), shifted by the track root. Other scales except Drums: one row of scale notes from the root, upper row dark; Drums: eight 4x2-cell drum pads (`p=(r>>1)*2+(c>>2)`, lowest bottom left, colours `DC`, note `DRUMS[p]+12*oct`), identical on both pads; Pad A sounds while held (one note at a time per drum), Pad B repeats while held (`kRep`, keyed by drum pad): `sched` calls `kRepFire` on the track's step ticks (`cnt%div===0`), and while stopped a setInterval of 15000/bpm*div ms fires. Pad A: sounds while pressed (`kA`, released through `releasePad(step,row)`); Pad B: latched toggle (`kB`). Velocity `t.kv` (10–127, default 100, saved), set by the Pad B right column as an 8-button slider (`ctrl` wrapper, levels `10+r*117/7`, shown in `rightCol`/`rightLbl`); output = track port/channel. `ctrl` is a wrapper that only passes Play/Stop, Mute (track mute; `kOn`/`kHit` stay silent for a muted track or `S.mall`), Mode (and its choices), Pad B Scale/Root, Swap (T1–T4 select tracks as usual); Pad A Oct−/Oct+ change `t.oct` directly. `render` compares `sg()` and releases all keys when track, scale, root, octave, channel or output change. Not saved, untested on hardware.
- Root: `S.root` is global, `t.rs` a per-track shift. Mode then Root−/Root+ (Pad B top 5/6, yellow, also on Chromatic) changes only the selected track (`ctrl`, ends Mode). The Root dropdown shows `eff(cur())` and moves all tracks by the same interval. `rootChg(list,fn)` adds +1 octave when a track's effective root goes B→C and −1 for C→B (clamped ±3, Drums skipped); used by the dropdown, global Root± and the per-track change.
- Oct−/Oct+ are toggles (pressing the armed button again cancels `S.om`). The on-screen Octave control is a dropdown (−3..+3). Syntax checked only.
- The Pad A hold-to-mute button was replaced by the velocity view.
- The first-run default of 8 steps was added and then removed. The default is always 16.
- Random modes use a shared track playhead because per-lane random looked like individual notes being picked.
- Obsolete on-screen buttons were removed whenever a pad button covers the function. Lane loop, mute and mode info is now shown only through LED colours.
- Clear mode is one-shot on purpose. Probability is rolled per step, not per note.
- The Mode button is the bottom button of the Pad A right column at the user's request. Prob (above Mode) selects random walk and Gate selects Jump during stepping selection.
- Jump is a switch beside the stepping mode, not an order. It takes over the bottom row of the note grid while on. It is a hold, not a one-shot: the playhead stays on the step while the pad is down.
- Accidentals shift by at most one semitone and the sharp/flat rules follow the scale degree, not the note name.
- Octave controls are armed: press Oct−/Oct+ first, then choose a note, lane, or track. Note and lane offsets are limited to ±3 octaves; note offsets clear when that note is toggled off/on. Note overrides and nonzero lane offsets use magenta indicators. These offsets apply to melodic and drum tracks.
- Instrument output and tempo divider are per track (settings row, selected track). Divided tracks keep their playhead between their steps; note lengths scale with the divider.
- A bug to remember: a global string replace that appends `S.am=0;` after `S.gv=false;S.pv=false;` also hit `accBtn` itself and made the toggle always switch on. Fixed with a `was` variable. Watch for this when doing bulk replaces.

## Open points and risks
- **Tempo divider mode on the pads** is only syntax checked.
- **Jump and the new right column order** are only syntax checked. Jump acts on the next scheduled step (up to the 120 ms lookahead later), and on release the last held step may already be queued. Release detection depends on each Launchpad model sending Note Off or velocity 0, which is untested.
- **New, untested on hardware:** per-track outputs (several devices at once) and the tempo divider. Only a syntax check was run. The settings row now has two more controls and has not been viewed on phones.
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


## Session changes (v05, 2026-11-01)
Workflow as before: edits by Python scripts with unique-anchor asserts, then a `new Function()` syntax check on the extracted script. **All of the following is syntax checked only, nothing tested in a browser or on hardware.** Files delivered: `twin-pad-sequencer.html` and `index.html` (manual, now titled v05 with release notes).

### Patterns (8 per track)
- **Data:** each track has `pats[8]` (null = never stored), `pi` (active index, default 0) and `cue` (-1 or index, runtime). The active pattern lives in the normal track fields; inactive ones are snapshots. Helpers (before the Persistence section): `PA` (typed-array fields: g, ln, acc, no, lm, loct, sm, lo, hi, vel, gate, prob, ldir and the per-lane arrays `LK`), `PS` (scalars: dir, scale, oct, div, jmp, rs), `snap`, `patLoad` (copies in place, so array identities and `pos`/`holds` keep working), `blank` (empty pattern; keeps the track's scale, oct, div, rs), `pser`/`pdes` (JSON), `patHas`, `patCol`, `patSwitch`, `patPress`, `patEnter`. **Not part of a pattern:** MIDI output (`so`, `port`), channel, mute, Key mode (`km`, `kv`, `kd`).
- **Pattern view (`S.pm`, not persisted):** Mode, then Pad B top row 3 or 4 (`ctrl0` → `patEnter`; buttons labelled Pat, cyan when active). Pad A grid rows 8..5 are tracks 1..4 and the columns are patterns 1..8; row 4 switches all four tracks. Pad B grid is dark. `pressPad` handles it before Key mode and Tempo divider mode. T1–T4 and Play stay active; Mode leaves; any other button leaves and then does its normal job (`ctrl0` header block); `cmBtn` and `apply` also clear it. `ctrl` lets everything through to `ctrl0` while `S.pm` (Key mode included).
- **Cue:** a pattern press sets `t.cue`; pressing it again, or pressing while stopped, calls `patSwitch` at once; pressing the active pattern cancels the cue. The switch is made in `sched` when the track's own tick counter `tc[ti]` is a multiple of `S.len` (so it follows Tempo ÷ and works for every stepping mode). `tc` is reset in `play` (not on Continue). After the switch `setTimeout(render,0)` refreshes the UI and `holds` of the track are cleared.
- **Copy:** in pattern view Vel (labelled Copy, `S.pcp`) then a pattern button copies the active pattern of that track (bottom row: of every track) into that slot. One-shot.
- **LEDs:** bright track colour = active, dim = has notes, flashing (white / dim, `BL` toggled by a 350 ms interval) = cued, bottom row white when all tracks share the pattern. The same colours are drawn on screen with the pattern number as cell text.

### Key mode: keyboard / drum pad layout
- Inside Key mode (not in the Mode selection) **Vel** (Pad A right column 7th) toggles `t.kd` (per track, saved, not in patterns); the label shows the target (**Drum** or **Keys**), bright orange on the drum layout. `kDr(t)=(scale==='Drums')!==!!t.kd`; it is part of the `sg()` signature (keys are released on a change). Mode, then Vel still leaves Key mode.
- **Mapping:** the drum pad layout (8 pads of 4x2 cells, lowest bottom left) plays General MIDI drum notes only on the Drums scale. On every other scale the pads play the scale notes from the root (the sequencer lanes' pitches at the track octave; C D E F G A B C on Chromatic). Pad B repeat and Pad A hold work the same. On the Drums scale with `kd` the layout is the piano (Chromatic layout: root from `eff(t)`). Per-lane octave offsets are not applied to the pads.

### Per-lane velocity, gate and probability
- **Data per track:** `lvel[128]`, `lgate[128]`, `lprob[128]` (index lane*16+step) and the flags `fvel[8]`, `fgate[8]`, `fprob[8]` (lane uses its own values). They are part of patterns (`LK`) and saved.
- **Access:** `vget(kind,s)`, `vset`, `vall` (all act on `S.vl`, the lane of the open view, or on the track when null), `vlane()`, and `lv(t,kind,r,s)` for playback. The first edit of a lane copies the track's 16 values into it and sets its flag.
- **Entering:** press a Pad B lane button (arms the loop edit), then Vel, Gate or Prob: `vlane()` reads `S.arm.r`; the status line names the lane. Switching directly between views keeps the lane. Closing the view clears `S.vl`.
- **In a lane view:** the pad columns, on-screen sliders and the master slider (Pad B right column, grey bar) edit that lane only. In the track view (`S.vl` null) the master slider and the Pad B buttons (`vall`) set the track values and clear the flags of all lanes for that kind. Step edits in the track view leave lanes with a flag alone.
- **Playback:** `sched` uses `lv` for velocity and gate. Probability: `ok(s,r)` rolls once per step shared by lanes without override, and once per lane and step for lanes with `fprob`.

### Save and load
- **Buttons:** `#saveall` (`twin-pad-all.json`), `#savemidi` (`twin-pad-midi.json`), `#saveptn` (`twin-pad-patterns.json`), one `#load`. `ser(part)` with part `'all'` (default, used by autosave), `'midi'` or `'ptn'`; `dl(part,name)` downloads.
- **MIDI group:** `conn`, `sync`, `models`, `devs`, and per track `ch` and `so`. **Pattern group:** `bpm`, `root`, `sel`, `swap`, `len`, and per track everything else (including `mute`, `km`, `kv`, `kd`, `pi`, `pats`, the lane arrays).
- **Load (`apply`):** only the fields present are applied; per track `ch`/`so` independently, the rest only when `t.g` is an array. Old files with a global `devs.so` still give tracks without `so` that output. `applyDevs()` runs after a load.

## Open points added by this session
- Everything above is untested beyond syntax: pattern cue timing (switch happens up to the 120 ms lookahead ahead of the display), pattern view on hardware, the Key mode layout toggle and drum-pad mapping, per-lane value views, partial load round trips (all/MIDI/patterns, old session files).
- Per-lane probability rolls separately per lane and step only for overridden lanes.
- A lane view has no marker on the hardware beyond the grey master bar and the status line.
- Pattern view uses the Mode/Scale combination; the Scale buttons keep their normal job otherwise.

## Session changes (v06, 2026-11-02): custom drum maps
Edits by Python scripts with unique-anchor asserts, then a `new Function()` syntax check. **Syntax checked only, nothing tested in a browser or on hardware.** Files delivered: `twin-pad-sequencer.html`, `index.html` (manual, titled v06 with a release note), `manual.md`, `drum-map-default.json` (example of the default map).

- **File format:** `{"name":…,"lanes":[{"lane":1-8,"label":"BD","note":0-127,"channel":1-16|null},…]}`, beautified. Channel `null`, `""` or missing = the track's channel. Label is cut to 2 characters (empty = default label). Lanes missing from the file keep the default. Errors reject the file (status line shows the reason).
- **State:** `S.dmap` is `null` (default) or `{name, m:[8 x {l,n,c}]}` with `c` 0–15 or null. Defaults: `DRUMS` notes and `DLB` labels BD SD CH OH CP LT MT CR (the default labels are my choice).
- **Functions (after `cur`):** `dnote(r)` (mapped or default note), `dch(t,r)` (mapped channel or `t.ch`), `dmParse(x)` (file/session format to internal, throws on errors), `dmOut()` (internal to file format). Also `kch(t,step,r)` (Key mode drum pad channel), `dmSet(m,msg)` (silences all tracks with `allOff` and `kOff()`, sets the map, renders, persists).
- **Applies to:** every track whose scale is Drums: `pitch` uses `dnote`; `sched` sends note-on/off on `dch(tr,r)` for Drums tracks; Key mode drum pads (`kbNote` uses `dnote`, `kOn`/`kRepStart` take a channel, `kHit` uses the entry's `e.ch`). On a non-Drums scale with the drum pad layout the pads use the track channel. Per-lane/per-note octave offsets still apply to the mapped notes. `allOff` sends CC 123 on the track channel and, for Drums tracks with a map, on every mapped channel.
- **Labels:** `rightLbl` shows the map's label on the Pad B lane buttons when the selected track is on Drums and a map is loaded (not in the slider views or Key mode, which have their own labels). Without a map the note name is shown as before. No hardware text, LEDs only.
- **UI:** in the MIDI devices grid (8th slot) a `.dmc` block: name `#dmname`, buttons `#dmload` (file input `#dmfile`) and `#dmdef` (back to default). `ui()` updates the name.
- **Persistence:** `ser`: the MIDI group (`'midi'`, `'all'`, autosave) contains `dmap` (`dmOut()` or null). `apply`: if `'dmap' in d` then `S.dmap=d.dmap?dmParse(d.dmap):null` (a null resets to default; files without the field keep the current map; an invalid map makes the load fail with a message). Pattern files do not contain it. Pattern scale changes do not affect the map (it is global).
- **Manual:** new "Drum maps" subsection in section 5 (also in the sidebar), plus features, saving, on-screen controls, lane label, `dmap` session field and release note v06 entries.

### Open points added by v06
- Untested: loading a map on real hardware, per-lane channels on a synth, Key mode drum pads with mapped channels, the Drum map control in the MIDI devices layout on phones.
- A map applies globally; there is no per-track map or multiple stored maps.
- Switching a track away from Drums while notes ring only silences the old channels on the next stop, mute or map change.

## Session changes (v07, 2026-11-04): toggle button behaviour
Syntax checked only. Files: `twin-pad-sequencer.html`, `manual.md`, `context.md` (`index.html` manual updated).
- **Toggles:** Mute (`S.mm`), Mode (`S.dm`), Oct−/Oct+ (`S.om`), Extend (`S.ext`), ♭/♯ (`S.am`) and the Vel/Gate/Prob views change state only when their own button is pressed. Nothing ends them any more: Mode stays on after a stepping mode, Jump, Root± or Key; Oct stays armed after a target; Extend re-arms after each extension. Pressing another toggle switches the active one off (`offAll()` clears `om`, `tdm`, `pm`, `pcp`, `dl`; each toggle function calls it first). Clear mode stays one-shot.
- **Mode sub-views:** pattern view (`pm`) and tempo divider mode (`tdm`) now live inside Mode (`dm` stays true). In them T1–T4 select the track, Vel is Copy (pattern view), other right-column buttons are idle; Mode leaves both. A lane picked for a per-lane stepping mode (`dl`) stays selected until pressed again or Mode ends. Lane buttons are ignored while Extend is on.
- **LEDs:** `topCol`/`rightCol` are wrappers around `topCol0`/`rightCol1`: an active toggle returns `RED()` (flashing red); second functions return `flc(colour)` (own colour pulsing). The old Mode colours (stepping colours, yellow/cyan BPM, Pat, Root, Jump, Key) were removed. The grid is never flashed. Phase `BL` (350 ms interval) now redraws whenever a toggle is active.
- **Open:** the current stepping mode and Jump state are no longer visible on the buttons. Untested on hardware: flash load (a full LED update every 350 ms).

### v07 fix: Key button cycle
`keyBtn` (Mode, then Vel) now cycles per track: sequencer -> `km` with `kd=false` (default layout of the scale) -> `kd=true` (other layout) -> sequencer (`km=false`, `kd=false`). Mode stays on. The Vel label while Mode is on names the next state (`nk`: Keys / Drum / Seq). With Mode off, Vel does nothing in Key mode (dark LED); the old Vel layout toggle in the `ctrl` wrapper was removed. Syntax checked only.

### v07: flashing step row and LED brightness
- **Bottom row flash:** while clear mode, mute mode or Jump (current track) is active the bottom grid row pulses (`flc` in `led`; class `fo` on screen, empty cells only). The blink interval also redraws for `S.cm` and `cur().jmp`.
- **Brightness:** `S.bri` 1–8 (default 8, saved in the MIDI group as `bri`). `padSend(pi,list,ex)` scales all LED colours by `bri/8`; in pattern view the bottom row of Pad A (11–18, `PEX`) is the slider and is exempt from dimming. `pressPad` in pattern view sets it. Launchpads only; on-screen colours are unchanged; no drag on screen. Syntax checked only.

### v07: Key velocity range
The Key mode velocity slider (Pad B lane buttons) uses the same levels as the step velocity master slider: `round((r+1)*127/8)`, i.e. 16, 32 ... 127 (`ctrl` wrapper, `rightCol1`, `rightLbl`). `kv` is clamped 1–127 on load; old saved values show as the lit levels up to them. Syntax checked below.

## Session changes (v08, 2026-11-05): character display, BPM/Oct swap
Syntax checked only. Files: `twin-pad-sequencer.html`, `manual.md`, `context.md` (`index.html` manual updated; the BPM/Oct positions and the display need adding there).
- **Button swap:** BPM−/+ are now Pad A top row 5/6, Oct−/+ Pad B top row 1/2. Implemented by remapping `(i,k)` at the top of the `ctrl0` top-row block (Pad A 5/6 <-> Pad B 1/2), so all old BPM/Oct logic is unchanged; `TL`/`TT`, `topCol`, `topCol0`, `topLbl` (Div label on Pad A 5/6), the Key mode wrapper (Oct direct on Pad B 1/2) were adjusted. LED base colours stay with the positions (Pad A 5/6 teal, Pad B 1/2 blue); armed Oct and active Mode flashes follow the functions.
- **Character display:** `S.gl` (`null` or `{i:1,c:'C#'}`, not saved). `FNT` 3x5 font (upper case, `r` lower case for Dr, `-`, `+`, `#`), `glOn(r,k)`, `RN`, `SAB` (scale abbreviations), `glyphFor(i,n)`. Glyph rows 5..1, columns 0-2 and 4-6. `ctrl` is now a wrapper: `ctrlX` (old `ctrl`) then `glyphFor`; `ctrlUp` ends the display (hardware release via `onPad` `m.up`, on-screen `pointerup/cancel/leave`, 700 ms after a keyboard click, window blur). `led` overrides the whole grid of the logical pad; `draw` overrides its cells (not shown in on-screen slider views). Triggers: Pad B Scale◀/▶ (not after Mode, where they open pattern view) and Root−/+ (also after Mode, showing `eff(t)`; not on Chromatic where they are ♭/♯, except in Key mode). Sharps only (`C#`).
- **Open:** release events for edge buttons on every Launchpad model are untested; display not seen on hardware.


## Session changes (v09, 2026-11-06): selection buttons
Edits by Python scripts with unique-anchor asserts, then a `new Function()` syntax check. **Syntax checked only, nothing tested in a browser or on hardware.** Files: `twin-pad-sequencer.html`, `manual.md`, `context.md` (`index.html` manual updated).
- **State:** `S.sv` = `null` or `{k:'oct'|'scl'|'root', v}`. Oct: `v` = octave delta −3..+3 (starts 0). Scale: `v` = index in `SN` (starts at the selected track's scale, wraps). Root: `v` = accumulated semitone steps from the selected track's effective root. `S.om` is gone.
- **Functions (before `mm`):** `selStart(k,d)` (clears other modes, steps the selection, `glSet`, render), `glSet` (sets `S.gl` for Pad B), `selEnd`, `rootBy(list,sd,glob)`, `selApply(w,a,b)` with w = `pad|lane|track|all`.
- **Buttons:** Pad B top 1/2 Oct, 3/4 Scale, 5/6 Root (post-remap roles unchanged inside `ctrl1`). `ctrl0` is now a head: with `S.sv` it routes destinations (Extend = All, T1–T4 = track, Pad B lane buttons for Oct only), lets Oct/Scale/Root buttons through, and ends the selection on any other button before `ctrl1` (the old `ctrl0` body) runs. `pressPad`: Oct applies to the note, or to the whole step (all notes in that column) for an empty pad; for Scale/Root a pad press only ends the selection. Mode then Scale still opens pattern view; Mode then Root starts a root selection (Mode ends); on Chromatic, Root buttons are ♭/♯ unless Mode is on.
- **Apply:** Oct adds the delta to note `no`, lane `loct` or track `oct` (clamped ±3). Scale sets the scale of the track or all tracks. Root: track = that track's `rs` shifted; All = `S.root` shifted (all tracks move by the same interval); octave carry `floor((eff+sd)/12)` (Drums skipped).
- **Display:** persistent while `S.sv` is set (`glyphFor` removed, `ctrlUp` is empty). `FNT` has digits 0–3. Oct: sign char (+, -, blank) and digit.
- **Mute all:** Extend (top row 8, both pads) replaces Play while mute mode is on (`toggleAll`; Key mode lets Extend through when `S.mm||S.sv`). Play no longer turns red; Extend is red while `S.mall`, green/red flashing in mute mode, label "All" in mute mode and during a selection. Clear mode still uses Play for "clear everything".
- **LEDs:** the active selection's buttons flash red; T1–T4, Extend (and lane buttons for Oct) flash as second functions.
- **Open:** untested on hardware. Pad presses during Scale/Root selection cancel without toggling a note (by design, to avoid accidental edits). Root selection on All tracks shifts rather than sets an identical root.
- **Drums excluded:** `selApply` with All skips tracks whose scale is Drums for Oct and Scale; `rootBy` already skips the octave carry for Drums (root has no effect on drum pitch). A single-track destination still applies to a Drums track.
- **Display timeout:** `glSet` restarts a 2 s timer (`glT`) that clears `S.gl` and renders; `S.sv` stays active (buttons keep flashing, destinations still work). Each Oct/Scale/Root press shows the display again.
- **Brightness slider moved:** the LED brightness slider (`S.bri`, `briCol`) is now the bottom row of Pad A in tempo divider view (`S.tdm`, set in `pressPad`; `padSend` exempts `PEX` while `S.tdm`), no longer in pattern view (bottom rows there are dark). Pad B's bottom row stays dark in divider view.
- **Playhead reset cue:** Mode then Mute (`ctrl1` `k===2` with `S.dm` → `rstCue`, toggle) sets `rc[ti]` (all tracks, not saved). In `sched`, when `rc[ti]&&tc[ti]%S.len===0` (`rst`, same moment as pattern cues) the flag is consumed: shared-playhead modes restart at step 1 (`ts=0`); every other lane gets `pos=0`, `pdir=1`, and `fr[ti][r]=1` if its range does not start at step 0. A free lane (`fr`, modes forward/reverse/ping-pong only) steps with `freeNext` over the whole length and plays its notes until the step falls inside `[el,eh]`, then `fr=0` and normal `nextStep` resumes. Jump holds win over the reset; `fr` is cleared by `play` (not Continue), `patSwitch`, shared modes and own random lanes. Mute shows label Reset in Mode and flashes white while a reset is cued. Syntax checked only.


## Session changes (v10, 2026-11-07): BPM display and fine steps
Edits by Python script with unique-anchor asserts, then a `new Function()` syntax check. **Syntax checked only, not tested in a browser or on hardware.** Files: `twin-pad-sequencer.html`, `manual.md`, `context.md` (`index.html` manual updated).
- **Press:** BPM−/BPM+ (Pad A top row 5/6, `ctrl1` `bpmPress(-1|1)`) change `S.bpm` by 1 (was 5) and call `bpmShow`. The `S.dm` (tempo divider) behaviour is unchanged; Key mode still greys the buttons out.
- **Hold:** `bpmPress` starts a 2000 ms timeout; then `bpmStep(d*10)` fires at once and every 500 ms (`bh` = `{t,iv}`) until `bpmHoldStop`. `ctrlUp` (hardware release via `onPad` `m.up`, on-screen `pointerup/cancel/leave`, 700 ms after a keyboard click, window blur) calls `bpmHoldStop`, so any edge-button release ends a hold. `setBpm` clamps 30–300.
- **External clock (`S.sync`):** no change and no hold timers; `bpmShow` displays the measured tempo `round(2500/ivl)` (or `S.bpm` before any clock pulse).
- **Display:** `S.gl={b:bpm}` (selection displays use `{i:1,c}`). `glPad(i)` says whether a pad is overridden (BPM: both pads, selections: Pad B); `glOn(r,k,i)` draws: Pad A hundreds digit in columns 5–7 (blank below 100), Pad B tens in columns 0–2 and ones in 4–6, rows 1–5. `FNT` now has digits 0–9. Every change restarts the 2 s timer (`glT`); `led` and `draw` use `glPad`/`glOn` with the logical pad index.
- **Open:** hold timing and release detection on each Launchpad model are untested; on-screen slider views do not show the display (as with selections).


## Session changes (v10, part 2, 2026-11-07): musical tempo divisions
Python script edits with unique-anchor asserts, then a `new Function()` syntax check. **Syntax checked only, untested in a browser or on hardware.** Files: `twin-pad-sequencer.html`, `manual.md`, `context.md` (`index.html` manual updated).
- **BPM hold:** now 1500 ms before the ±10 repeat starts (`bpmPress`), then every 500 ms.
- **Divisions:** `DV` = 12 entries `{l,u}` sorted fast to slow: 32T, 32, 16T, 32D, 16, 8T, 16D, 8, 4T, 8D, 4, 4D; `u` = step length in twelfths of a 16th (4, 6, 8, 9, 12, 16, 18, 24, 32, 36, 48, 72). Assumption: the range "32nd to 4" means 1/32 to quarter notes, each with T and D variants. Track field is now `dv` (index, default 4 = 16th) instead of `div`; saved as `dv`. `odv(n)` converts old `div` 1–16 (nearest `u`; 1→16, 2→8, 3→8D, 4→4, 5+→4/4D). Patterns store `dv` (`PS`, `blank`, `pdes`, `ser`, `apply`).
- **Scheduler:** `sched(cnt,t,d)` is still called once per 16th. For each track it fires every event at positions p = multiples of `u` inside [12*cnt, 12*cnt+12) by calling `trkEvt(tr,ti,time,dd)` (the old body of the per-track map; `dd=d*u/12`), with sub-step timestamps `t+(p-a)/12*d`. No event = playhead returned unchanged. All tracks share one grid counted from the start; changing a divider never restarts playback. Key mode drum repeat (`kRepFire`) is called per event; the stopped-state retrigger interval uses `15000/bpm*u/12`.
- **Buttons:** Pad A top row 3/4 are Div−/Div+ (`ctrl1`: `selStart('div',∓1)`); with Mode on (`S.dm`) they are Len−/Len+ (`setLen`). They flash as second functions in Mode (`topCol`), labels via `topLbl`/`topTtl`/`TL`/`TT`. Mode then BPM± still opens the divider slider view (now 12 positions: columns 1–12 of the 16 across both pads, `pressPad` sets `dv=step`, cells show the label); the LED brightness slider is unchanged.
- **Selection:** `S.sv={k:'div',v}` (index, starts at the selected track's `dv`, clamped 0–11, − faster, + slower). Destinations: T1–T4 = that track, Extend = all tracks **including drums** (`selApply`). The Div buttons are `sb` pass-through buttons in `ctrl0` (other selections switch to Div). Red flashing for Pad A 3/4 while selected; the Root branch of the flash test now checks `S.sv.k==='root'`.
- **Display:** `S.gl={b:label}` (same two-pad layout as the BPM display, right-aligned: first character on Pad A columns 5–7, others on Pad B columns 0–2 and 4–6), 2 s timer `glT`. `FNT` gained `T`.
- **Settings row:** the Tempo ÷ dropdown lists the 12 labels (value = index).
- **Open:** sub-step timing at fast divisions (32T = 1/3 step) with many tracks and sysex-sized LED updates is untested; very fast divisions give short gates (`min(dd*gate,dd-1)`).

### v10 fix: steady playhead display for triplets and dotted notes
`sched` no longer returns per-step positions. It collects every track event of the step, sorts them by time and pushes one display snapshot per event to `q` at the event's exact time (`dispCur` = current playhead per track, reset in `play`); `tick` and `onClock` just call `sched`. Before, one snapshot per 16th step showed only the last event, so the playhead skipped steps and moved irregularly on triplets and dotted notes, although the MIDI timestamps were already steady (checked with a jsdom simulation of `sched`). Syntax and simulation only; not tested on hardware.

### v10 (part 3): old tempo divider sliders removed, Opt view
Version stays v10. Mode, then BPM−/+ (Pad A top row 5/6, labelled **Opt**, title "Options (LED brightness)") still sets `S.tdm`, but the view now holds only the LED brightness slider (bottom row of Pad A, `briCol`, `PEX` exempt from dimming); the four top-row track sliders, their `pressPad` branch, LEDs and on-screen bars/labels are gone. `tdm` keeps its name in code (T1–T4 still select the track in it, Mode leaves). Tempo dividers are now set only by Div−/Div+ plus a track button or Extend, or the Tempo ÷ dropdown. Manual (`manual.md`, `index.html`) and the in-app help were updated. Syntax checked only.

### v10 (part 4): tempo divisions up to two whole notes
`DV` now has 20 entries (u in twelfths of a 16th): 32T 4, 32 6, 16T 8, 32D 9, 16 12, 8T 16, 16D 18, 8 24, 4T 32, 8D 36, 4 48, 2T 64, 4D 72, 2 96, *1T 128, 2D 144, *1 192, *2T 256, *1D 288, *2 384. Whole notes and above are labelled with a leading `*` (`*1` = whole note, `*2` = two whole notes; plain `2` = half note); no dotted two-whole-note value. `FNT` has a small `*`. Default index stays 4 (16th). Saved sessions and patterns now store `du` (the `u` value) so the list can change; `dvLoad` also reads the earlier `dv` index (old 12-value list, `OLDU`) and the original `div` 1–16. Selection and dropdown use `DV.length`. Manual, index and help updated. Syntax checked only.


## Session changes (v11, 2026-11-08)
Version bumped to v11 (app title and heading, manual, `index.html` with a v11 release note). All four files were updated. Syntax checked only.

### v11 part 1: octave tiers, channel sliders, reset cues, Jump all
Python edits with unique anchors, `new Function()` syntax check only. Untested in a browser or on hardware. Files: `twin-pad-sequencer.html`, `manual.md`, `context.md` (`index.html` manual updated).
- **Octave tiers:** sounding shift = `no` (note) + `loct` (lane) + `oct` (track) + `S.oa` (All, global, saved in the pattern group as `oa`, not applied to Drums). `selApply` now SETS the value (absolute, no accumulation; 0 resets). `pitch` adds `S.oa` for melodic tracks. Marking: note `no`≠0 magenta (LED, and class `oo` on screen), lane `loct`≠0 magenta (existing), track button violet [100,60,127] when `oct`≠0. Default track octaves are now 0 (were +1 / −1 for tracks 2 / 3) so no track starts marked. Key mode keyboards ignore `S.oa`.
- **Channel sliders:** in the options view (`S.tdm`) grid rows 5–8 of both pads are per-track channel sliders (`pressPad`: `tracks[7-r].ch=step`, `allOff` first; `chCol`, LED and screen show the number). `PEX` exempts these rows from LED brightness dimming.
- **Reset cues:** Mode, Mute (`rstCue(false)`) cues the selected track only (`rc[S.sel]`). Mode, Extend (`S.rx`, Extend flashes red, label All), Mute (`rstCue(true)`) sets `rxa` = selected track; when that track loops (`tc%len===0`) `trkEvt` sets `rf` for all tracks and each resets at its next event. `play` clears both. Extend in Mode no longer leaves Mode.
- **Jump all:** with the selected track's Jump on and neither Mode nor mute mode, Extend toggles `S.ja` (red when active, label All); `jumpTo` pushes holds onto all tracks. Cleared when Jump is switched off. Note extend is not available while Jump is on.

### v11 part 2: background operation
Scheduler timer moved to a Web Worker (`startTimer`/`stopTimer`, Blob worker, 25 ms; falls back to `setInterval`). `tick` uses a 500 ms lookahead while `document.hidden` (120 ms otherwise) and calls `pump()` (playhead display queue + Launchpad LEDs, no DOM `draw`) because requestAnimationFrame pauses in hidden tabs; `loop` also uses `pump`. `visibilitychange` re-renders. External clock needs no change (MIDI events). Untested: Blob workers from `file://`, long background runs, iOS/MIDIWeb. The `blur` handler still clears Jump holds and Key mode notes. Other `setInterval` timers (flash blink, stopped-state drum repeat, BPM hold) remain throttled when hidden.

### v11 part 3: LEDs in the background
The worker is now created once at load (`initWorker`) and sends a 25 ms tick and a 350 ms blink. Tick: `tick` (scheduler) when playing without external clock, `pump` when playing on external clock; `tick` always calls `pump` (the playhead display queue and LEDs no longer depend on requestAnimationFrame or `document.hidden`). Blink: `blink()` (was a main-thread `setInterval`; flashing now runs at full speed in the background, `draw` skipped while hidden). Without a worker both fall back to `setInterval`. Untested on hardware.

### v11 part 4: clear mode on Mode hold
Hold Mode (Pad A right column bottom, hardware or on screen) for 2 s: `modeHoldStart` (called from `ctrl1`, where Mode toggles as usual) starts a 2 s timeout that calls `cmBtn` (not in Key mode); `ctrlUp` (any release, blur) cancels it via `modeHoldStop`. On-screen right-column buttons now call `ctrlUp` on pointerup/cancel/leave. The on-screen CLR button and its handlers were removed (RND stays). Clear mode (`S.cm`, one-shot): Mute, T1–T4, all lane buttons, Extend and the bottom row of the grid flash red (`RED()`); a head block in `ctrl1`: Mute cancels, Extend = `clearAll` (replaces Play), targets act as before, any other button cancels and then does its normal job. Play no longer clears everything. Extend is labelled All in clear mode. Manual, index and help updated. Untested on hardware: release detection for the Mode button on each Launchpad model.

### v11 part 5: clear mode moved to Mode, then Oct−
The Mode hold (part 4) did not work and was removed (`modeHoldStart/Stop`, hold wiring). Clear mode is now Mode, then Pad B top row 1 (Oct−; `ctrl1`: `k===5&&S.dm` after the Pad A 5/6 remap calls `cmBtn`). Label CLR and title in `topLbl`/`topTtl`, and the button flashes as a Mode second function. Oct+ with Mode still starts an octave selection. Everything else from part 4 (red flashing targets, Mute cancels, Extend = all, any other button cancels) is unchanged. Not available in Key mode (Oct± change the track octave there).

### v11 part 6: CLR flashing, RESET button
- Clear mode: the flashing red button is now Oct− (Pad B top row 1, `topCol` RED test `S.cm&&((i===1&&k===1)||k===8)`), not Mute. Mute has no special role: it is an ordinary "other button" (cancels clear mode, then toggles mute mode). Oct− pressed again in clear mode (`ctrl1` head, `n===91&&i===1`) leaves it. Label CLR also while `S.cm`.
- RESET button (`#reset`, bottom right below the footer): after `confirm()`, sets `RS=true` (blocks `persist`), clears the pending save, removes the `twin-pad-sequencer-v1` localStorage key, stops playback and notes, then `location.reload()`, so the app starts as on a first load (no autosave, not connected). Pads leave programmer mode via `beforeunload`. Syntax checked only.
