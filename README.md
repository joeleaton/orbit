# Orbit

A dual-engine granular effect. Two orbiting grain engines read from a
rolling buffer of your audio, modulate and chop it, and feed each other
through cross-feedback. You play it by dragging the orbits around the
canvas.

By Joel Eaton.

macOS on Apple Silicon (M1/M2/M3/M4), macOS 11 or later.
Ships as **Audio Unit**, **VST3** and **Standalone**.

---

## Download

Get the latest version from **[Releases](https://github.com/joeleaton/orbit/releases/latest)**.

Direct link to the current version:
**[Orbit-1.1.zip](https://github.com/joeleaton/orbit/releases/download/v1.1/Orbit-1.1.zip)**

Unzip it and follow the install steps below (they're also in the bundled README).

---

## Documentation

- **[User Guide](docs/USER_GUIDE.md):** first five minutes, the
  interface, playing the canvas, gravity wells, sound-design recipes,
  troubleshooting.
- **[Reference](docs/REFERENCE.md):** every parameter with ranges and
  defaults, DSP details, canvas mappings, factory presets, changelog.

---

## What's new in 1.1

- **Easier-to-read interface.** Labels and values are noticeably larger, secondary text is much brighter, and section headings (Modulation, Granular, Gate · Pan, Global) are bigger and bolder.
- **Title-case labels.** "FM Depth", "X-Feedback", "Dry/Wet" and so on, instead of all caps.
- **Preset reference markers.** After you load a preset, turning a dial leaves a small tick on its outer edge, in the dial's colour, showing where the preset had it. Handy for experimenting and finding your way back.
- **More breathing room.** The control panels have more vertical spacing and the default window is a little larger (1140 × 780). The minimum size is now 1020 × 620 so nothing overlaps.
- **A better starting sound.** The default/Init state now gives you something to hear and see straight away: Position 0.3 / 0.7, Size 0.3 on both engines, Engine B Freq 5 Hz, Dry/Wet 0.8.
- **New docs.** The User Guide and Reference are now readable online: see the links on the [project page](https://github.com/joeleaton/orbit).

---

## Install (short version)

Unzip `Orbit-1.1.zip` and drag from its `Plug-ins/` folder:

- `Orbit.component` → `~/Library/Audio/Plug-Ins/Components/` (Logic, MainStage, GarageBand)
- `Orbit.vst3` → `~/Library/Audio/Plug-Ins/VST3/` (Ableton Live, Cubase, Reaper, Bitwig, Studio One, …)
- `Orbit.app` → `/Applications` (Standalone, no DAW needed)

### First-run gotcha (important)

Orbit is ad-hoc signed, so macOS marks it as quarantined. **The
plug-ins will silently fail to load in your DAW** until you remove the
quarantine flag. In Terminal, after copying the files into place:

```
xattr -dr com.apple.quarantine ~/Library/Audio/Plug-Ins/Components/Orbit.component
xattr -dr com.apple.quarantine ~/Library/Audio/Plug-Ins/VST3/Orbit.vst3
xattr -dr com.apple.quarantine /Applications/Orbit.app
```

For the Standalone you can instead use
**System Settings → Privacy & Security → Open Anyway** after the first
launch attempt is refused. Plug-in bundles don't get that prompt, so
the `xattr` command is the only fix for those.

---

## Credits

- DSP, UI, plug-in: Joel Eaton.
- Built with [JUCE](https://juce.com).
