# Sinusoidal model – class notes (transcription)

Handwritten notes for the slides [Topic 5: Sinusoidal Model](../lectures/05-Sinusoidal-model.pdf).
Transcribed from `sinusoidal_1.jpeg`. `[?]` marks words that are hard to read;
*(slide: …)* points to the slide each note belongs to.

---

## Page 1 (`sinusoidal_1.jpeg`)

### Why a sinusoidal model *(slide 3: From STFT to a higher-level representation)*

- → In the **full spectrum there is a lot of redundancy**.
- ↳ The spectrum of one sinusoid can be summarised by just **[A₀, f₀, phase]** → strictly speaking
  *(slides 4–5: Sinusoidal model · Computing the spectrum of a sinewave)*.
  - Sketch (top right): a window spectrum — tall main lobe with a dotted line at its centre, smaller side lobes
    on both sides. *(The whole lobe is just the window transform shifted to f₀ and scaled by A₀/2, so three
    numbers describe it.)*

### Resolution *(slides 6–7: Windowing shapes every sinusoidal peak · Choosing M is a modeling decision)*

- ↳ [?] is not [more?] FFT sizes *(i.e. a bigger N alone does not solve it — see slide 10)*.
- · [Chirps?] can be approximated [as] linear systems, straight lines
- should be, [but] there is not enough frequency resolution.

### Listening to the result *(slide 8: Real musical sounds contain many overlapping peaks)*

- → You would listen [to] non-harmonic sounds:
  - Sketch: a wavy spectrum with two peaks marked with ×.
  - *(Own question, in Spanish: "¿Cómo suena un non-harmonic sound?" — how does a non-harmonic sound sound?)*

### Threshold *(slide 9: Peak detection)*

- → **Spectrogram** → be careful with the **appearance of side lobes if the threshold is not set properly**
  *(a too-low threshold `t` lets side-lobe maxima be detected as fake peaks/tracks).*

### Harmonic sounds *(preview of Topic 6: Harmonic model)*

- For harmonic sounds, **f₀ is the fundamental frequency**.
