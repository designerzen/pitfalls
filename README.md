# pitfalls

Microtonal tuning for equal divisions of the octave (EDO). Give it a scale in large/small step notation and it returns the frequency and fractional MIDI pitch of every degree, plus the cent offsets needed to retune a 12-note keyboard.

## Installation

```bash
npm install github:designerzen/pitfalls
# or
pnpm add github:designerzen/pitfalls
```

## Scale notation

A scale is a sequence of `L` (large) and `s` (small) steps, a large step size and a small step size. The number of divisions of the octave is the sum of the steps.

| Scale | Sequence | Large | Small | EDO | Notes |
| --- | --- | --- | --- | --- | --- |
| Major | `LLsLLLs` | 2 | 1 | 12 | 7 |
| Gorgo-6 | `LLsLLL` | 3 | 1 | 16 | 6 |

See [MOS scales](https://en.xen.wiki/w/MOS_scale) for more on the notation.

## Usage

```typescript
import { parseEdoScaleMicroTuningOctave } from "pitfalls"

// Gorgo-6 in 16-EDO, starting on MIDI note 60 in octave 3
const pitches = parseEdoScaleMicroTuningOctave(60, 3, "LLsLLL", 3, 1)

pitches.octdegfreq(3, 2)  // 297.936  (Hz of degree 2 in octave 3)
pitches.octdegmidi(3, 2)  // 62.25    (fractional MIDI pitch)
pitches.midiMappings      // [[60,0],[62,25],[64,50],[65,25],[67,50],[69,75]]  ([MIDI note, cents])
pitches.octaveTuning      // Map of MIDI 60-71 to cent offsets, for retuning a 12-note octave
```

Results are cached, so calling it again with the same arguments returns the same `Pitches` object.

## API

### `parseEdoScaleMicroTuningOctave(baseNoteMidi, rootOctave, sequence, large, small, rootFrequency = 440)`

Builds and caches a `Pitches` object. `rootFrequency` is the tuning of A4. `edoScaleMicroTuningOctave` is the same function without `rootFrequency`, always using 440 Hz.

### `EdoScale`

`new EdoScale(large, small, sequence, medium?)` holds the step pattern. Read `edivisions` (divisions of the octave), `length` (notes per octave) and `divisions` (step offsets of each degree).

### `Intervals`

`new Intervals(scale)` works out each degree's ratio to the root. `ratio(i)`, `intervalLabel(i)`, `intervalRatio(i)`, `intervalError(i)` and `nearestDegreeTo(ratio, threshold?)`.

### `Pitches`

`new Pitches(scale, intervals, tuning, midiStart, rootOctave, keyMappings)` maps every degree in octaves 0-8:

- `octdegfreq(octave, degree)` and `octdegmidi(octave, degree)`: frequency and fractional MIDI pitch, with degrees counted from 1
- `octdeg(degree)`: turns a degree beyond the first octave into `[octave, degree]`
- `freq(index)` and `degree(index)`: lookup by running index across all octaves (`getFrequency` and `getDegree` on the object `parseEdoScaleMicroTuningOctave` returns)
- `midiMappings` and `octaveTuning`: the root octave as MIDI notes with cent offsets

### `keyMappings`

A `Map` from scale length to the default keyboard layout passed to `Pitches`.

## License

AGPL-3.0-or-later. Copyright (C) 2025 Rob McKinnon.

## See also

The [manifest](package.json) supplies the current package version and source export paths; [index.ts](index.ts) defines the public tuning functions. This package has no build or test script. Use a TypeScript-aware host/bundler and check the receiving instrument's tuning support when integrating fractional MIDI pitches.

- [audiobus](https://github.com/designerzen/audiobus), which uses this for its microtonality transformer
- [Equal temperament](https://en.wikipedia.org/wiki/Equal_temperament)
