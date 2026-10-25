# STFT – Xavier Serra's video lectures (week 4)

Summary of the week-4 videos on the Short-Time Fourier Transform from the channel
[Xavier Serra (@XavierSerraC)](https://www.youtube.com/@XavierSerraC/videos)
(course *Audio Signal Processing for Music Applications*). Built from the English captions of each video.
Matches the slides [04-STFT.pdf](../lectures/04-STFT.pdf) and the class notes [stft_notes.md](../class_notes/stft_notes.md).

| Code | Video | Type |
|---|---|---|
| 4T1 | [STFT 1](https://www.youtube.com/watch?v=OOeWYbUtfNI) | Theory: equation + analysis windows |
| 4T2 | [STFT 2](https://www.youtube.com/watch?v=GkQeUgL_Zr4) | Theory: M, N, H, time–frequency compromise, inverse STFT |
| 4P1 | [Windows](https://www.youtube.com/watch?v=JVAClT43rzQ) | Programming: computing and measuring windows |
| 4P2 | [STFT](https://www.youtube.com/watch?v=juUuBru5h6Q) | Programming: `stft.py` in sms-tools |
| 4D1 | [Spectrogram](https://www.youtube.com/watch?v=zDjdoW35jgw) | Demo: soprano voice (Sonic Visualiser + sms-tools) |
| 4D2 | [Analyzing a sound](https://www.youtube.com/watch?v=qYXtGUQAvYE) | Demo: piano phrase |

---

## 4T1 – STFT 1: equation and analysis window

**Why the STFT.** Real sounds cannot be represented by a single spectrum; they change in time. The STFT
is a time-varying DFT. Analogy: a spectrum is a photograph, the STFT is a video.

**Equation.**

$$
X_l[k] = \sum_{n=-N/2}^{N/2-1} w[n]\, x[n + lH]\, e^{-j2\pi kn/N}, \qquad l = 0, 1, \dots
$$

- `l` = frame number, `H` = hop size, `w[n]` = analysis window. The rest is the DFT.
- The output is a **sequence of spectra**, all of the same size, each with magnitude and phase.
- The time index goes from −N/2 to N/2−1 so the frame is **centered around zero** (zero-phase windowing):
  no time shift and therefore no extra phase slope.

**Windowed sinusoid.** Write the cosine as two complex exponentials (±k₀). By linearity the DFT splits into two
DFTs of the window, each shifted in frequency:

$$
X[k] = \tfrac{A_0}{2} W[k - k_0] + \tfrac{A_0}{2} W[k + k_0]
$$

→ The spectrum of a windowed sinusoid is **the transform of the window, shifted to the sinusoid's frequency
and scaled by half its amplitude** (plus its mirror at −k₀). This is why the window matters so much.

**Describing a window.** Real, symmetric around the origin. Two numbers characterise its spectrum:
- **main-lobe width** (in bins), and
- **highest side-lobe level** (in dB below the main lobe).

Each window is a different compromise between these two. All windows are sums of cosines in time
→ sums of sinc functions in frequency.

| Window | Definition | Main lobe | Highest side lobe | Comment |
|---|---|---|---|---|
| Rectangular | 1 inside the window | 2 bins | −13.3 dB | Spectrum is a sinc |
| Hann | raised cosine, `0.5 + 0.5 cos(2πn/M)` | 4 bins | −31.5 dB | Sum of **3 sincs** |
| Hamming | raised cosine with a small step at the edges | 4 bins | −42.7 dB | Same main lobe as Hann, much lower side lobes, **but the side lobes do not decay much** away from the main lobe |
| Blackman | sum of cosines | 6 bins | −58 dB | Starts to be useful for many audio applications |
| Blackman-Harris | sum of 4 cosines | 8 bins | −92 dB | −92 dB is below the noise floor of 16-bit audio → the side lobes "can't be heard"; almost no side lobes |

- The "2 bins" of the rectangular window are measured **with N = M** (no zero-padding). Zero-padding only makes
  more bins appear because it samples the same shape more densely (smoother plot).
- Comparing rectangular / Hamming / Blackman on the same sound fragment: Blackman gives the smoothest spectrum
  with the most clearly separated peaks (the harmonics).

---

## 4T2 – STFT 2: window size, FFT size, hop size, inverse STFT

**Window choice.** The same frame of a violin looks very different with a rectangular vs. a Blackman window.

**Window size M.** Same sound and window, M = 128 vs. M = 1024: the short window shows very little detail;
the long one shows much more of what is going on in frequency.

**Even vs. odd M.** M = 32 vs. M = 31: the magnitude spectra look the same, but the **phase is different**.
- With even M the window cannot be perfectly centered at 0 (different number of samples on each side) → a phase slope.
- With odd M there is a sample exactly at 0 and the same number on each side → perfectly symmetric → **zero phase**.
- → **Use odd-size windows whenever possible.**

**FFT size N.** Independent of M thanks to zero-padding. Example: M = 512 oboe frame with N = 512 vs. N = 2048:
the zero-padded spectrum is much smoother, which makes it easier to identify peaks.

**Hop size H.** The window advances H samples after each DFT. The sum of the shifted windows
$A_w = \sum_l w[n - lH]$ should be **a constant** (a straight line):
- M = 201, H = 100 (half the window): the sum oscillates → a **modulation that will be heard** in the resynthesis.
- M = 201, H = 50 (one quarter): flat in the middle (only the boundaries are distorted) → good.

**Time–frequency compromise** (piano phrase):
- Small window → crisp attacks (good time resolution), blurry harmonics (poor frequency resolution).
- Large window (×4, M = 1024) → well-defined horizontal lines (harmonics) but less crisp attacks.

**Phase spectrogram.** What is usually plotted is the **derivative of the phase**: flat regions (stable phase)
show the stable peaks/harmonics; large phase changes show up as a different colour. Harder to read, but useful.

**Inverse STFT (overlap-add).**
1. Inverse DFT of every frame `X_l` → a windowed output frame `y_l[n] = x[n + lH] w[n]`.
2. Shift each frame to its position `lH` and add.
3. $y[n] = x[n] \sum_l w[n - lH]$ → if the sum of the windows is a constant (1 after normalising), output = input;
   otherwise there is an audible **modulation/distortion**.

**Complete analysis–synthesis system:** x[n] → window → FFT → magnitude + phase → IFFT → windowed frames →
overlap-add → y[n]. With a correct window and hop size, y[n] is identical to x[n] (demonstrated by listening to the piano).

---

## 4P1 – Windows (programming)

Script that gets a window and computes its spectrum:

1. `M = 63`, `w = get_window('hann', M)` (scipy).
2. Compute the half-sizes of the window, taking care of even/odd `M`.
3. **Zero-phase buffer:** create an FFT buffer of `N = 512` zeros; put the second half of the window at the start
   and the first half at the end (window centered on sample 0).
4. `X = fft(buffer)`; magnitude in dB with `20*log10(abs(X))`, replacing values below `eps` to avoid `log(0)`;
   phase with `angle(X)`.
5. Undo the zero-phase arrangement (fftshift) to plot the main lobe in the middle.
6. Plot with the x-axis in bins (`× M/N`, so zero-padding doesn't change the scale) and magnitude normalised to 0 dB.

Measurements on the plots confirm the theory: Hann 4 bins / ≈ −31 dB, Hamming 4 bins / ≈ −42 dB,
Blackman 6 bins / ≈ −58 dB, Blackman-Harris 8 bins / ≈ −92 dB (the y-axis has to go down to −100 dB to see them).
The phase of the centered window is 0 (modulo 2π jumps) → it is a **zero-phase window**.

Second script: a 5000 Hz sinusoid at 44100 Hz, 101 samples, analysed with `dftAnal(x, w, N)` from
`dftModel.py` (which already does zero-phase windowing and zero-padding). The spectrum is the window's transform
centered at 5000 Hz — the theory result in practice.

---

## 4P2 – STFT (programming, `stft.py`)

`software/models/stft.py` has three functions: `stft` (analysis + synthesis), `stftAnal` and `stftSynth`.

- Inputs: sound `x`, sampling rate `fs`, window `w`, FFT size `N`, hop size `H`.
- Core: a `while` loop with a pointer `pin` that advances by `H` until the end of the sound. For each frame:
  - take the fragment, call `dftAnal` → positive half of the magnitude and phase spectra;
  - call `dftSynth` → windowed time fragment;
  - **overlap-add** it into the output array.
- Extra code pads the beginning and end so the analysis starts and ends exactly at the sound's limits.

Example with a flute (> 80 000 samples, Hamming window): `mX.shape` → 238 frames × 512 bins (half spectrum).
`mX[50]` plots one frame (harmonics, quieter high harmonics, noise); `pX[50]` the unwrapped phase.
`plt.pcolormesh(np.transpose(mX))` shows the spectrogram with time on the x-axis and frequency on the y-axis.

`stft_function.py` (used by the GUI) does analysis + synthesis, plots input, magnitude, phase derivative and output,
and writes `piano_stft.wav`, which sounds the same as the original.

---

## 4D1 – Spectrogram of a soprano voice (demo)

Sound: a soprano note, high pitch with **vibrato** (frequency oscillation).

- **Sonic Visualiser**, Blackman, M = 1024: horizontal lines = harmonics, and their oscillation = vibrato.
- M = 256: the individual harmonics disappear; you see a smooth envelope = the **formants**
  (resonances of the vocal tract, which let us tell vowels apart). The formants change little over time; the harmonics change more.
- Rectangular window: high noise floor, few harmonics visible, vertical artefacts → Blackman is much better for this sound.
- **sms-tools DFT** at 0.5 s, M = N = 1024 Blackman: clear harmonic peaks + phase + inverse (windowed) signal.
  M = N = 256: much smoother, less information. M = 801, N = 1024: fewer samples but still good resolution and a smooth
  shape thanks to zero-padding.
- **sms-tools STFT**, M = N = 1024 Blackman:
  - **H = 256 (M/4)** → the reconstruction is the same as the original. *For Blackman the hop must be at most one quarter of the window.*
  - **H = 1024 (= M)** → a burst of sound every frame: an audible **modulation at the frame rate**, because the windows do not overlap enough to add up to a constant.

---

## 4D2 – Analyzing a piano sound (demo)

Piano phrase of 5 notes, sms-tools STFT, Blackman.

- Lower pitch than the voice → bigger window: **M = 1501** (odd → centered at 0, good for phase; make it a habit),
  **N = 2048** (next power of 2 for the FFT), **H ≈ 325** (≈ M/4) → perfect reconstruction.
- **Phase spectrogram:** clear vertical lines at the **attacks** (the phase is disrupted) and horizontal structure
  during the notes (harmonics keep phase continuity).
- **Magnitude spectrogram:** harmonics as red lines; at the attack more energy and more harmonics, then they decay,
  the high harmonics first.
- Zooming in shows the grid: vertical bars = one per frame (H = 325 samples); thin horizontal bands thanks to N = 2048.
- **M = 201, N = 256, H = 50:** still a perfect reconstruction (same H/M ratio), but now narrow vertical bars
  (better time resolution) and tall boxes (worse frequency resolution) → **time–frequency compromise**.
- **DFT at the attack (≈ 1.54 s) vs. 100 ms later (≈ 1.64 s)**, M = 1501, N = 2048:
  - attack: harmonics not well defined yet, more high-frequency energy (higher "noise floor");
  - steady state: clear, well-resolved harmonic peaks, low harmonics dominate.

---

## Key takeaways

- STFT = a sequence of DFTs of windowed, overlapping frames.
- The spectrum of a windowed sinusoid is the window's spectrum shifted to its frequency → choose the window by
  **main-lobe width vs. side-lobe level**.
- Odd M → zero-phase analysis; N ≥ M (power of 2) → zero-padding for a smooth spectrum; H must make the
  windows add up to a constant (**≤ M/4 for Blackman**), otherwise an audible modulation appears.
- Short windows: time resolution; long windows: frequency resolution.
- With magnitude **and** phase and a correct hop size, the inverse STFT (overlap-add) recovers the original sound exactly.
