# STFT – class notes (transcription)

Transcribed from `1000108528.jpg` and `1000108529.jpg`. `[?]` marks words that are hard to read.

---

## Page 1 (`1000108528.jpg`)

> *(Top of the page, crossed out and unrelated: "Master Thesis Project — 31st of August 2027 → multiply → and presentation — RESEARCH QUESTIONS!")*

### STFT

$$
X_l[k] = \sum_{n=-M/2}^{M/2-1} x[n + lH]\, w[n]\, e^{-j2\pi kn/N}
\qquad l = 0, 1, \dots \qquad k = -N/2, \dots, N/2-1
$$

- `H` = hop size, `w[n]` = analysis window, `l` = frame index.

- ↳ The sound in this frame has to be **stable**.
- ↳ **Time resolution** is much higher than the sound transformation rate [?]
  - → hop size should be around **20 ms** (temporal resolution).
- Δf Hz → you can distinguish → plot of sonority vs. [those?] sounds.
- → The only phase we are interested in is the one around 0; the phase should be 0 if you unwrap it and [dewrap?] it.
- → 2 bins → without zero padding.

### Hann window

$$
w[n] = 0.5 + 0.5\cos(2\pi n / M) \;\rightarrow\; \text{3 sinusoids in reality!!}
$$

$$
W[k] = 0.5\,D[k] + 0.25\,\big(D[k-1] + D[k+1]\big)
$$

- **Hamming window** → is not decreasing in the long term.
  - ↳ default
  - ↳ much better side-lobe level.
- **Blackman** → 5 sinc functions.
- **Blackman-Harris window** → side lobes at **−92 dB** → below the order of the distortion.
  - ↳ for a stable sinusoid this would be ideal.

---

## Page 2 (`1000108529.jpg`)

- → Check the harmonics of the trumpet.
  - ↳ Blackman → we need more periods → we should add more zero-padding.
- → Hamming → **onset detection!!**
- Interpolation → adding zero padding.
- → The width of the side lobes is 1 bin → the phase must be an artifact for a side lobe.
- ↳ **Important question** → harmonics / side lobes / phase → **width!** (1 bin, 4 bins).

*(Drawing: a wave whose amplitude rises and falls — amplitude modulation.)*

- ↳ A sinusoid modulating another one
  - ↳ can be seen as a **product** or as an **addition**.
- ↳ **Timbre** → overall shape of the spectrum.
  - (resonance → formant (spectral shape) → resonance cavity)
- **M must be odd** → better to identify the phase.
- Hamming / Hanning → overlap [50%?] *(number overwritten)*
- Blackman → overlap → 50% [?]
  - ↳ We are hearing the modulation of the hop sizes.

*(Drawing: a series of overlapping humps — "created with Blackman" — next to a sketch of a single window shape.)*

- + At the beginning and the end we are smoothing.
