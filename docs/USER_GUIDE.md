# Orbit — User Guide

A friendly tour of Orbit, from first sound to deeper sound design.

If you just want a parameter-by-parameter reference, see
[REFERENCE.md](REFERENCE.md).

---

## 1. What Orbit Is

Orbit is a stereo granular effect built around two parallel engines,
**A** and **B**, linked by cross-feedback. Feed it any audio (vocals,
synths, drums, field recordings) and it keeps a rolling 10-second
buffer of what it hears. Each engine then:

1. reads overlapping **grains** from that buffer at its own position and size,
2. **modulates** them with an LFO or audio-rate oscillator,
3. **chops** them through a tempo-synced probability gate and pans them,
4. feeds its output back into the **other** engine.

```
            ┌──────────── 10 s stereo buffer ────────────┐
            ▼                                            ▼
     ┌─────────────┐        cross-feedback        ┌─────────────┐
     │  ENGINE A   │ ◀──────────────────────────▶ │  ENGINE B   │
     │ Mod · Grain │                              │ Mod · Grain │
     │ Gate · Pan  │                              │ Gate · Pan  │
     └──────┬──────┘                              └──────┬──────┘
            └──────────────┐            ┌────────────────┘
                           ▼            ▼
                    (A + B) → Dry/Wet → Master → out
```

Everything you hear comes down to two things: **where** each engine
reads from the buffer, and **how much** the engines feed each other.

---

## 2. Your First Five Minutes

1. **Load Orbit** on an audio track. Something with sustain (a pad, a
   loop, a vocal) works best the first time. In the Standalone, pick
   your input and output under **Options → Audio/MIDI Settings**.
2. **You'll see two orbits**: a mint ring (A) and a pink ring (B). Orbit
   starts with them at different positions and speeds, with Dry/Wet at
   0.8, so you hear the effect straight away.
3. **Drag an orbit.** Click inside a ring and drag. Left/right sets where
   in the buffer it reads (**Position**); up/down sets **Jitter** (up =
   more chaos).
4. **Drag the size handle**, the small dot at 3 o'clock on the ring.
   Pull out for longer grains, push in for shorter, tighter ones.
5. **Turn up X-Feedback** in the Global column. The dotted line between
   the orbits thickens as the engines start building on each other.
6. **Drop a gravity well.** Click **Gravity 0/4** at the top of the
   canvas, then click the canvas. Nearby orbits bend toward it.
7. **Try a preset.** Use the ◀ ▶ arrows in the top bar, or open the
   preset menu and load **Gravity Field**, which comes with four wells
   already placed.

---

## 3. The Interface

```
┌──────────────────────────────────────────────────────────────────┐
│ ORBIT │ [Preset ▾] ◀ ▶ Save Delete          Out ▬▬▬  ⚙  CPU 2.1% │ TOP BAR
├──────────────────────────────────────────────────────────────────┤
│ Orbit v1.1              [ Gravity 0/4 ]                   Field  │
│                                                  Dist · Overlap  │
│                    (  A  ) · · · · · (  B  )                     │ CANVAS
│ A                                                             B  │
│ Freq · Size · Pos                                Freq · Size · Pos│
├────────────────────────┬───────────┬─────────────────────────────┤
│ ENGINE A               │  Global   │ ENGINE B                    │
│ Modulation │ Granular  │ X-Feedback│ Modulation │ Granular       │ CONTROLS
│            │ Gate · Pan│ Dry/Wet   │            │ Gate · Pan     │
│            │           │ Master    │            │                │
└────────────────────────┴───────────┴─────────────────────────────┘
```

### Top bar

| Control | What it does |
|---|---|
| **Preset menu** | Every factory and user preset. |
| **◀ ▶** | Step through presets (wraps around). |
| **Save** | Save the current sound under a name. |
| **Delete** | Delete the selected preset from disk. |
| **Out** | Output level meter. |
| **⚙** | Opens the Tweaks panel (spectrogram and gravity toggles). |
| **CPU** | Live CPU use: mint under 5%, amber under 15%, pink above. |

### Canvas

The dark area in the middle. It shows both orbits, their playheads and
grain dots, the cross-feedback line between them, the spectrogram
backdrop and any gravity wells. The canvas is also a controller (see
section 4).

Corner readouts:

- **Top-left:** Orbit version.
- **Top-right:** **Field**: distance between the orbit centres, and how much they overlap.
- **Bottom corners:** Freq, Size and Pos for Engine A (left) and B (right).

### Control panels

| Column | Sections |
|---|---|
| **Engine A / Engine B** | **Modulation** (Waveshape, Mode, Freq, Depth, Jitter, FM Depth) · **Granular** (Position, Size, Density, Spray) · **Gate · Pan** (Division, Probability, Pan Mod) |
| **Global** | X-Feedback, Dry/Wet, Master |

The two engines have identical controls and are fully independent.

### Preset reference markers

After you load or save a preset, each dial remembers the preset's
value. Turn a dial away from it and a small tick, in the dial's own
colour, appears on the outer edge of the dial to show where the preset
had it. Turn the dial back to that point and the tick disappears. It's
an easy way to experiment and still find your way home.

### Tweaks panel

Click **⚙** in the top bar to open a small panel at the top-right of the canvas:

- **Spectrogram:** show or hide the frequency backdrop (turning it off saves CPU).
- **Gravity:** turn gravity warping on or off (same as the Active/Bypassed button).

---

## 4. Playing the Canvas

The canvas isn't just a picture. Every part of an orbit is a handle you
can drag, and it stays in sync with the dials both ways.

| Drag this | How to find it | It controls |
|---|---|---|
| **Ring** | Click anywhere inside the ring | **Position** (left/right) and **Jitter** (up = more) |
| **Size handle** | Small dot at 3 o'clock on the ring | **Size** (grain length) |
| **Playhead** | The bright dot circling the ring | **Freq** (mod rate): small moves at low rates, big jumps at high rates |

Hovering shows a readout next to whatever you're over (Pos · Jitter,
Size or Freq), and the cursor changes to a hand.

### What the visuals mean

- **Grain dots** around each ring: one per grain (**Density**). They
  scatter more as **Spray** goes up.
- **Wobble:** with **Pan Mod** on, the orbit drifts side to side. A
  dashed half-arc shows which side the pan is biased toward.
- **Dotted line between orbits:** the cross-feedback amount. It gets
  thicker and brighter above 0.5.
- **Amber glow:** appears where the two orbits overlap, brighter with
  more overlap and more feedback. It means the engines are interacting heavily.

---

## 5. Gravity Wells

You can drop up to four gravity wells on the canvas. They bend the
orbit rings, grain dots and playheads toward them.

**Placing and removing**

1. Click **Gravity 0/4**. The button turns white and reads
   **Gravity · Placing**, and the cursor becomes a crosshair.
2. Click the canvas to drop a well. Repeat, up to four.
3. Click a well to remove it (hovering shows **Click to Remove**).

**Gravity buttons** (shown once a well exists)

- **Gravity N/4:** turns placing mode on or off and shows how many wells there are.
- **Active / Bypassed:** turns the warping on or off. Bypassed wells stay visible but faded.
- **Clear:** removes every well.

Wells are saved with presets. Load **Gravity Field** to see a set-up example.

---

## 6. Sound Design Cookbook

### Subtle texture doubling
- Engines at nearby positions (A 0.35, B 0.45).
- Small Size (0.1–0.15), moderate Density (8–12).
- Low X-Feedback (0.1–0.2), Dry/Wet 0.3–0.4.
- Slight opposing Pan Mod (−0.2 / +0.2) for width.

### Rhythmic granular chopper
- Fast Freq (4–12 Hz), AM mode, Square or Saw.
- High Density (24–48), small Size (0.05–0.1).
- Division 1/8 or 1/16, Probability 0.3–0.5.
- Low X-Feedback (≈0.1) to keep it tight.

### Evolving drone / pad wash
- Very slow Freq (0.02–0.2 Hz), FM mode.
- Large Size (0.4–0.9), low Density (2–4), high Spray (0.6–1.0).
- High X-Feedback (0.6–0.85).
- Probability at or near 1.0 (gate always open).
- One or two gravity wells near the orbits for gentle movement.

### Metallic / industrial
- Audio-rate Freq (100–500 Hz), FM mode, Gristle waveshape.
- High Jitter (0.6–0.9).
- FM Depth 36–72 semitones.
- X-Feedback 0.4–0.6, Dry/Wet ≈0.7.

### Spatial movement
- Pan Mod at opposite extremes (−0.8 / +0.8).
- Medium sizes so the orbits are big on screen.
- Gravity wells off-centre to pull the orbits unevenly.
- Low X-Feedback so the movement stays clean.

---

## 7. Layout & Window Size

- **Drag the bottom-right corner** to resize the window, from 1020 × 620 up to 1920 × 1200.
- **Ctrl/Cmd + scroll** over the canvas changes the split between the
  canvas and the control panels.

---

## 8. Performance Tips

- **Density** is the biggest CPU cost. 8–16 grains is plenty for most sounds; 64 is the maximum.
- **Spectrogram:** turn it off in the Tweaks panel when you don't need it.
- **Zero latency:** Orbit adds no latency to your signal chain.

---

## 9. Troubleshooting

| Problem | Try this |
|---|---|
| **No effect heard** | Check Dry/Wet isn't at 0 and Master isn't at −60 dB. Make sure audio is actually reaching the track. |
| **Distorted output** | Lower X-Feedback (0.8+ can saturate), or pull Master down a few dB. |
| **Orbits barely moving** | Freq may be very low (under 0.1 Hz). That's expected; turn it up. |
| **Gravity wells do nothing** | Check the Active/Bypassed button at the top of the canvas. |
| **My wells weren't saved** | Wells save with presets. Save the preset after placing them. |
| **A factory preset didn't update** | Factory presets aren't overwritten if you already have one with that name (except **Init**, which always matches the current defaults). Delete the old file from the presets folder to get the new version. |
| **Meter moves with no input** | High feedback can leave a little residual noise. Set X-Feedback to 0 to confirm. |
| **Plug-in doesn't appear in my DAW** | See the install notes in the [README](../README.md): the plug-in needs its quarantine flag removed after copying. |

---

Joel Eaton — [joeleaton.co.uk](https://joeleaton.co.uk)
