# Orbit — Reference

Parameter-by-parameter reference and how it all works under the hood.
For a friendly tour, read [USER_GUIDE.md](USER_GUIDE.md) first.

---

## Signal Flow

```
input ──▶ 10 s stereo ring buffer
                │
       ┌────────┴────────┐
       ▼                 ▼
   ENGINE A          ENGINE B
   GristleOsc        GristleOsc        ◀── each engine's output feeds the
   StochasticGrain   StochasticGrain       other on the next block
   ProbabilityGate   ProbabilityGate       (X-Feedback, tanh soft-clipped)
       │                 │
       └──────┬──────────┘
              ▼
      (A + B) × 0.5 ──▶ Dry/Wet ──▶ Master ──▶ out
```

Stereo in, stereo out. Both engines process both channels. The stereo
image comes from per-grain panning, gate pan randomisation and Pan
Mod, not from splitting left and right between the engines.

---

## Parameter Index

### Per-engine (A and B)

Each engine has the same set of parameters, prefixed `a_` or `b_`.

#### Modulation

| Display | ID | Range | Default A / B | Notes |
|---|---|---|---|---|
| Waveshape | `*_waveshape` | Sine, Triangle, Saw, Square, Gristle | Sine / Sine | Modulation waveform |
| Mode | `*_mod_mode` | AM, FM | AM / AM | Amplitude or frequency modulation |
| Freq | `*_mod_freq` | 0.01–2000 Hz | 1.2 / 5.0 | Skew 0.3: about half the dial is below 5 Hz |
| Depth | `*_mod_depth` | 0–1 | 0.5 / 0.6 | Modulation intensity |
| Jitter | `*_jitter` | 0–1 | 0.5 / 0.5 | Pink-noise wobble on the mod rate. Also the orbit's height on the canvas |
| FM Depth | `*_fm_depth` | 0–96 semitones | 12 / 18 | Pitch range in FM mode. Skew 0.5 |

#### Granular

| Display | ID | Range | Default A / B | Notes |
|---|---|---|---|---|
| Position | `*_grain_pos` | 0–1 | 0.3 / 0.7 | Read position in the buffer. Also the orbit's left/right position |
| Size | `*_grain_size` | 0.001–1 | 0.3 / 0.3 | Grain length. Skew 0.5. Also the orbit radius |
| Density | `*_grain_density` | 1–64 | 8 / 14 | Grains per second (whole numbers) |
| Spray | `*_grain_spray` | 0–1 | 0.2 / 0.3 | Random drift of the read position |

#### Gate · Pan

| Display | ID | Range | Default A / B | Notes |
|---|---|---|---|---|
| Division | `*_chop_div` | 1/2, 1/4, 1/8, 1/16, 1/32 | 1/8 / 1/8 | Beat subdivision for the gate |
| Probability | `*_chop_prob` | 0–1 | 0.5 / 0.5 | Chance the gate opens on each step (1 = always) |
| Pan Mod | `*_pan_mod` | −1 to +1 | −0.3 / +0.4 | Stereo pan modulation bias |

### Global

| Display | ID | Range | Default | Notes |
|---|---|---|---|---|
| X-Feedback | `cross_feedback` | 0–0.98 | 0.3 | Cross-feedback between engines |
| Dry/Wet | `dry_wet` | 0–1 | 0.8 | Blend of original and processed |
| Master | `master_gain` | −60 to +6 dB | 0 dB | Output gain. Skew 2.0: more resolution near 0 dB |

### Gravity wells (saved with presets)

| Display | ID | Range | Default | Notes |
|---|---|---|---|---|
| Well Count | `g_well_count` | 0–4 | 0 | Number of wells placed |
| Active | `g_well_active` | 0 or 1 | 1 | Gravity on / bypassed |
| Well N X / Y | `g_well_N_x`, `g_well_N_y` | 0–1 | 0.5 | Normalised canvas position (N = 1–4) |

### Skew, in plain terms

A skew below 1 gives more dial travel to the low end of the range. For
example, Freq (0.3) spends about half its travel below 5 Hz, which is
good for fine LFO control. A skew above 1 (Master, 2.0) gives more
travel near the top, so there's more precision around 0 dB.

---

## DSP

### GristleOsc: modulation

One per engine, running from 0.01 Hz (slow LFO) to 2 kHz (audio rate).

- **AM:** the modulator becomes a 0–1 gain envelope on the grains (tremolo through chopping).
- **FM:** the modulator becomes a pitch-ratio multiplier on grain
  playback speed (metallic, industrial).

Waveshapes: **Sine**, **Triangle**, **Saw** (falling), **Square**
(±1), and **Gristle**, which is white noise sampled and held 4× per
cycle, then soft-clipped through `tanh(3x)` for a blown-circuit sound.

**Jitter** adds 1/f (pink) noise to the rate, using the Voss-McCartney
method with 16 rows. At full jitter the rate can swing ±50%. All
continuous parameters are smoothed (about 5 ms) so automation doesn't click.

### StochasticGrain: granular

- Reads from the shared 10-second stereo buffer, with up to 64 overlapping grains.
- Each grain has a Hann window, linear-interpolated playback for pitch
  shifting, and its own Gaussian stereo pan.
- **Brownian scanning:** the read position drifts randomly with Spray
  and is pulled back toward Position (×0.999 per block), so textures
  evolve instead of looping.
- The output is scaled by `1/√(active grains)` so high densities don't clip.

### ProbabilityGate: chopper

Syncs to the host tempo, with an internal tempo if the host doesn't
send one. On each subdivision it rolls against Probability: open
passes audio, closed is silence. Transitions are smoothed (about 2 ms)
and each gate event re-randomises the pan position.

### Cross-feedback

At the end of each block, A's output becomes B's feedback input and
B's becomes A's. On the next block each engine adds that feedback to
its grains. The feedback gain is smoothed, and a `tanh` soft-clipper
on each engine stops runaway. At 0.1–0.3 you get subtle interaction;
from 0.6 up the engines build recursively.

---

## Canvas Mappings

| Gesture | Parameter | Mapping |
|---|---|---|
| Drag ring, X | Position | Canvas left → right = 0 → 1 |
| Drag ring, Y | Jitter | Canvas bottom → top = 0 → 1 |
| Drag size handle | Size | Distance from centre, squared for a smooth feel |
| Drag playhead | Freq | Each radian of rotation multiplies Freq by e^0.6 (≈1.8×) |

**Orbit radius** = `20 px + (0.32 × canvas height − 20 px) × size^0.6`.

**Pan wobble** = `PanMod × 14 px × sin(time × 0.5 + engine × 1.2)`.

### Gravity

Each well pushes nearby points toward it with a smoothstep falloff:

| Property | Value |
|---|---|
| Influence radius | 350 px (no effect beyond) |
| Max displacement | 50 px per well, scaled by element |
| Element strength | Ring 60% · grain dots 50% · playhead 90% |
| Overshoot guard | Displacement capped at 80% of the distance to the well |

Gravity is visual only. It doesn't change the audio directly.

---

## Factory Presets

| Preset | Character |
|---|---|
| **Init** | The parameter defaults: two offset orbits at different speeds, moderate feedback, Dry/Wet 0.8. Always rewritten to match the current defaults. |
| **Dark Ambient** | Slow modulation (0.15 / 0.08 Hz), large grains, heavy feedback (0.85), wide stereo. Washy drones. |
| **Rhythmic Chop** | Fast modulation (8 / 12 Hz), high density (32 / 48), low gate probability. Aggressive and rhythmic. |
| **Industrial Metallic** | Audio-rate FM (220 / 330 Hz), high jitter, deep FM depth (48 / 60 semitones). Harsh and metallic. |
| **Frozen Texture** | Near-static modulation (0.03 / 0.02 Hz), very large grains, gate fully open. Glacial and suspended. |
| **Stereo Drift** | Strong opposing pan modulation (−0.9 / +0.85). Wide movement, gentle processing. |
| **Gravity Field** | Four gravity wells in an uneven diamond, X-Feedback 0.45. Shows off the spatial warping. |

### Preset files

| | |
|---|---|
| macOS location | `~/Library/Audio/Presets/Joel Eaton/Orbit/` |
| Format | `.gfpreset`: XML snapshot of the full plug-in state |
| Updates | New factory presets install automatically. Existing files with the same name are left alone, except **Init** |

---

## Window

| | |
|---|---|
| Default size | 1140 × 780 |
| Resize range | 1020 × 620 to 1920 × 1200 |
| Canvas/panel split | Ctrl/Cmd + scroll over the canvas |

---

## Format

- Audio Unit, VST3 and Standalone, for macOS on Apple Silicon (macOS 11+).
- Built with JUCE 8 / C++17.
- Zero latency.

---

## Changelog

### 1.1
- Larger, easier-to-read text throughout, with brighter secondary text and bigger section headings.
- Labels in title case (for example "FM Depth", "X-Feedback", "Gate · Pan").
- Preset reference markers: after loading a preset, a tick on each dial's edge shows the preset value once you move it.
- More vertical breathing room in the control panels, and a larger default window.
- New starting sound: Position 0.3 / 0.7, Size 0.3, Engine B Freq 5 Hz, Dry/Wet 0.8.

### 1.0
- First release.
