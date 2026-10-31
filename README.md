# Twin Pad Sequencer

This step sequencer runs in your web browser and is played with one or two Novation Launchpads. It sends MIDI notes to a synthesizer or DAW. It is a single self-contained HTML file with vanilla JavaScript, no build step and no dependencies. It also works with mouse or touch only, with no Launchpad at all.

## Disclaimer
This app was created with AI assistance. The code is modified and edited manually as well. This app does not contain any trackers or malware and can run completely offline without any interaction with the internet. After asking for permission from the user, the software is able to interact with MIDI devices connected to or MIDI software running on the host system.
You use this app at your own risk. The creator of this app, Mark Dammer takes no liability for any damage to person, animal, property, hardware or software caused by the use of this software.

## Quick start

1. Open [twin-pad-sequencer-latest.html](https://mark-orion.github.io/Twin-Pad-Sequencer/twin-pad-sequencer-latest.html) directly in a browser **(best Chromium based like Chrome, Edge or Chromium)** (from the web, disk or from `localhost`). The browser needs Web MIDI and SysEx support. On iOS this requires the MIDIweb app.
2. Click **Connect MIDI** and allow MIDI access, including SysEx.
3. Open **MIDI devices** and choose the Launchpad **MIDI** port (not the **DAW** port) for Pad A, and for Pad B if you have a second pad. Choose the matching Launchpad model and a **Sound output** that goes to your synthesizer or DAW.
4. Press **Play** and start entering notes.

On first connect the app tries to pick the ports for you: the first Launchpad for Pad A, the second for Pad B, and a Sound output that is not a Launchpad for connecting to instruments or DAW. The app makes no sound by itself.

## Features

### Sequencing
- **4 tracks**, each with **8 note lanes** and **16 steps**. Pad A shows steps 1–8 and Pad B shows steps 9–16.
- **Sequence length** of 1–16 steps. Notes beyond the length are kept and return when you lengthen the sequence. Changing the length never restarts playback.
- **Held notes** that last several steps.
- **Per-lane loop ranges** for polymetric patterns: every lane can loop over its own start and end step.
- **Five stepping modes** per track: forward, reverse, ping-pong, random and random walk. Any single lane can override the track's mode.
- **Per-step velocity, gate length and probability**, drawn on the pads or with exact on-screen sliders. A master slider sets all 16 steps at once.
- **Mute** lanes, tracks, steps or the whole app. **Clear** a track, lane or step. **Randomize** a track.

### Pitch
- **Scales** per track: minor and major pentatonic, the seven diatonic modes (Ionian, Dorian, Phrygian, Lydian, Mixolydian, Aeolian, Locrian), **Chromatic** and **Drums** (General MIDI drum map). A shared **Root** note applies to all tracks.
- **Sharps and flats** on the Chromatic scale. Lanes run C D E F G A B C. Single notes or whole lanes can be sharpened or flattened, following the rule that E and B cannot be sharpened and F and C cannot be flattened.
- **Octave control at three levels**, each from −3 to +3: per track, per lane and per note. Press **Oct−** or **Oct+** first, then choose a note pad, a lane button or a track button. The offsets add up and also apply to drum tracks.
- **MIDI channel** per track. Drums default to channel 10.

### Timing and sync
- Internal clock with lookahead scheduling using Web MIDI timestamps, 30–300 BPM.
- **External MIDI clock** (24 PPQN) with Start, Continue and Stop messages from a chosen clock input.

### Hardware and interface
- **One or two Launchpads.** With one pad, a **Swap** button switches between steps 1–8 and 9–16 together with the second set of controls.
- **Supported models** (different models can be mixed):

  | Choice | Hardware |
  |---|---|
  | LP Mini MK3 | Launchpad Mini MK3 (default) |
  | LP X | Launchpad X |
  | LP Pro | Launchpad Pro and Pro MK2 |
  | LP X-Y Mode | Original Launchpad, X-Y layout |
  | LP Drum Mode | Original Launchpad, Drum Rack layout |

  The Launchpad Pro MK3 is not supported.
- **Everything on the hardware is also on screen.** The on-screen pads mirror the Launchpad LEDs and buttons, and the layout adapts to phones, tablets and desktops, including phone landscape.

### Saving
- **Autosave** in the browser a moment after every change, with automatic MIDI reconnect on load.
- **Save / Load** a session as `twin-pad-session.json` for backups and for moving between computers. Files from older versions load with defaults for anything new.

## Documentation

The full manual covers getting started, every button and mode, and a reference section (defaults, hardware buttons, scales, drum map, LED colours, protocols and the session file format):

- [Manual (Markdown)](manual.md) Manual in markdown format.
- [Manual (HTML)](index.html) The full manual in HTML format is included in the homepage file.

## Files

| File | Purpose |
|---|---|
| `twin-pad-sequencer-latest.html` | The app (latest version) |
| `twin-pad-sequencer-latest.sha256` | SHA256 checksum for integrity checks |
| `twin-pad-sequencer-latest.md5` | MD5 checksum for integrity checks |
| `manual.md` | Manual (latest version) |
| `context-latest.md` | Project context and design notes (latest version) |
| `index.html` | HTML source of Twin Pad homepage |

## Known limitations

- Autosave uses `localStorage`, which is separate for `file://` and `localhost`. Use **Save** for backups.
- The Play button is only on the Pad A top row on screen, so it scrolls out of view with the pads.

## © Mark Dammer, Forres, Scotland 2026
### No obligation, but if you like Twin Pad, you can always buy me a coffee <https://buymeacoffee.com/markdammer>
