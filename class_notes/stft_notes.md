# STFT – class notes (transcription)

Handwritten notes for the slides [Topic 4: The Short-Time Fourier Transform](../lectures/04-STFT.pdf).
Transcribed from `1000108528.jpg` and `1000108529.jpg`. `[?]` marks words that are hard to read;
*(slide: …)* points to the slide each note belongs to.

---

## Page 1 (`1000108528.jpg`)

> *(Top of the page, crossed out and unrelated: "Master Thesis Project — 31st of August 2027 → multiply → and presentation — RESEARCH QUESTIONS!")*

### STFT equation *(slide: Short-Time Fourier Transform equation)*

$$
X_l[k] = \sum_{n=-M/2}^{M/2-1} x[n + lH]\, w[n]\, e^{-j2\pi kn/N}
\qquad l = 0, 1, \dots \qquad k = -N/2, \dots, N/2-1
$$

- `H` = hop size, `w[n]` = analysis window, `l` = frame index.

### Why short-time analysis? *(slides: Why short-time analysis? · Hop size)*

- ↳ The sound in this frame has to be **stable** (the STFT assumes the signal is locally stable inside the frame).
- ↳ **Time resolution** is much higher than [how fast the sound changes?]
  - → hop size should be around **20 ms** (temporal resolution).

### Windowed sinewave / analysis window *(slides: Transform of a windowed sinewave · Analysis window)*

- Δf Hz → you can distinguish → plot of sonority vs. [those?] sounds
  *(main-lobe width decides whether two close sinusoids can be told apart).*
- → The only phase we are interested in is the one around 0 (where the magnitude is significant);
  the phase should be 0 if you unwrap it and [dewrap?] it *(zero-phase / centered window, see the phase-convention note on the equation slide).*
- → **Rectangular window**: main lobe of **2 bins** → without zero padding.

### Hann window *(slide: Hann window — main lobe 4 bins, side lobes −31.5 dB)*

$$
w[n] = 0.5 + 0.5\cos(2\pi n / M) \;\rightarrow\; \text{3 sinusoids in reality!!}
$$

$$
W[k] = 0.5\,D[k] + 0.25\,\big(D[k-1] + D[k+1]\big)
$$

(`D[k]` is the transform of the rectangular window, a sinc; so the Hann spectrum is the sum of 3 shifted sincs.)

### Other windows *(slides: Hamming · Blackman · Blackman-Harris)*

- **Hamming window** → its side lobes do not decrease in the long term.
  - ↳ default
  - ↳ much better side-lobe level (−42.7 dB, lower first side lobe than Hann).
- **Blackman** → 5 sinc functions (main lobe 6 bins, side lobes −58 dB).
- **Blackman-Harris window** → side lobes at **−92 dB** → below the order of the distortion.
  - ↳ for a stable sinusoid this would be ideal.

---

## Page 2 (`1000108529.jpg`)

### Window choice in practice *(slides: Window choice changes the spectrum · FFT size)*

- → Check the harmonics of the trumpet.
  - ↳ Blackman → we need more periods inside the window → we should add more zero-padding.
- → Hamming → **onset detection!!**
- Interpolation → adding zero padding *(zero-padding interpolates the spectrum, it does not add information).*
- → The width of the side lobes is 1 bin → the phase must be an artifact for a side lobe.
- ↳ **Important question** → harmonics / side lobes / phase → **width!** (1 bin, 4 bins).

### Modulation and timbre

*(Drawing: a wave whose amplitude rises and falls — amplitude modulation.)*

- ↳ A sinusoid modulating another one
  - ↳ can be seen as a **product** or as an **addition**.
- ↳ **Timbre** → overall shape of the spectrum.
  - (resonance → formant (spectral shape) → resonance cavity)

### Window size and hop size *(slides: Even-odd size window · Hop size)*

- **M must be odd** → better to identify the phase.
- Hamming / Hanning → overlap [50%?] *(number overwritten)*
- Blackman → overlap → 50% [?]
  - ↳ We are hearing the modulation of the hop sizes
    *(if the overlapped windows do not sum to a near-constant value, the output is amplitude-modulated at the hop rate).*

*(Drawing: a series of overlapping humps — "created with Blackman" — next to a sketch of a single window shape.)*

- + At the beginning and the end we are smoothing.
