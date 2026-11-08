# Harmonic model – class notes (transcription)

Handwritten notes for the slides [Topic 6: Harmonic model](../lectures/06-Harmonic-model.pdf).
Transcribed from `harmonic_1.jpeg`. `[?]` marks words that are hard to read;
*(slide: …)* points to the slide each note belongs to.

---

## Page 1 (`harmonic_1.jpeg`)

**TOPIC 6: HARMONIC MODEL**

### Harmonic vs inharmonic instruments *(slides: Inharmonic vs harmonic sinusoidal tracks · Sinusoids, partials, and harmonics)*

- Piano → harmonics are **stretched**.
- Air instruments oscillate in a harmonic way.
- Grand piano → ↑ string ~ ↑ harmonic *(longer strings → closer to a harmonic series).*

### Harmonic model vs sinusoidal model *(slides: Harmonic model · From sinusoidal to harmonic representation)*

$$
f_r[n] = r \cdot f_0[n]
$$

- ↳ Applied in **single-source / source-separated** signals.
- ↳ Potential for **transformations / interpretation**.
- ↳ Retuning of the same notes in different octaves to be exactly the same → for not having **beating**.
- ↳ **Vibraphone → not harmonic** (sound is produced by a metal bar).

*(Drawing: a flat bar → the same bar with a tube hanging underneath, "→ to fix" → "tubes of the length of the fundamental frequencies".)*

- ↳ The [cymbals?] is the one who have more **inharmonicity**.

### F0 and pitch *(slide: Fundamental frequency and pitch)*

- f0 ≠ pitch → ↑ loud → **impression of higher pitch**.

### Time-domain F0 detection *(slides: F0 detection with autocorrelation · Autocorrelation examples: oboe vs piano · YIN algorithm)*

- → All tuners are based on the **periodicity** → zero-crossing rate.

*(Drawing: an irregular waveform whose envelope decays into a clean sinusoid.)*

- → Piano tuner → inspection in the **frequency domain** → **sympathetic resonances** *(boxed)*.
  - ↳ Phases of piano are crazy.
- **YIN algorithm** → version of **autocorrelation**.

### Missing fundamental *(slide: F0 detection from spectral peaks — "F0 can be missing or weak")*

- Low notes of pianos cannot oscillate the fundamental frequency → our **ear-brain creates this fundamental** that we hear.
  - → translation [of?] frequencies → time order → send impulses.
