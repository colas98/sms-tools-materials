# Topic 5: Sinusoidal Model – slide-by-slide explanation

Slides: [05-Sinusoidal-model.pdf](../05-Sinusoidal-model.pdf) ·
Class notes: [sinusoidal_notes.md](../../class_notes/sinusoidal_notes.md) ·
Videos: [sinusoidal_videos.md](../../youtube_videos/sinusoidal_videos.md) ·
Lab: [E5-Sinusoidal-model.ipynb](../../exercises/E5-Sinusoidal-model.ipynb)

Sources, in order of priority: the slides → class notes → sms-tools code → own explanation.
Text marked *(own explanation)* does not come from the course materials. Video mappings are inferred
from the video titles only (captions not available yet) and are marked *(unverified)*.

**Slides 1–2 (title, roadmap).** The topic goes: model idea → spectral peaks → peak detection → tracks →
synthesis → examples + Lab 5.

---

## Slide 3 — From STFT to a higher-level representation

**What it says.** The STFT keeps a full complex spectrum per frame. The sinusoidal model keeps only selected
peaks and links them into tracks. We lose some information but gain abstraction and flexibility.
Pipeline: samples → STFT frames → spectral peaks → sinusoidal tracks. *Core idea: find peaks, link peaks,
synthesize tracks.*

**Explanation.** The STFT is a *generic* representation: it describes any signal, but with N/2+1 complex
values per frame, most of which are redundant for tonal sounds (a single sinusoid occupies a whole main lobe
of several bins plus side lobes). The sinusoidal model is a *parametric* representation: it assumes the sound
is made of sinusoids and stores only their parameters. Fewer numbers, and each one means something
(a frequency you can transpose, an amplitude you can scale). The price: anything that is not a sinusoid
(noise, sharp attacks) is lost — that is what Topic 7 (residual) recovers.

**From class notes.** "In the full spectrum there is a lot of redundancy."

---

## Slide 4 — Sinusoidal model

**What it says.**

$$
y[n] = \sum_{r} A_r[n]\cos\!\big(2\pi f_r[n]\, n + \varphi_r[n]\big)
$$

`r` = component index, `A_r[n]` instantaneous amplitude, `f_r[n]` instantaneous frequency, `φ_r[n]` phase.
Figure: magnitude spectrogram of `bendir.wav` with the sinusoidal tracks in black.

**Explanation.** This is the same sum of sinusoids as in Topic 2, with one key difference: amplitude and
frequency are **functions of time**. A real note has vibrato (f changes), and attacks/decays (A changes).
Each black curve on the figure is one `r`: one track whose frequency drifts over time.
*(Own explanation:)* written this way, `f_r[n]` is in cycles per sample (f/fs); in Hz the argument is
`2π f_r n / fs`, as on slide 17.

**From class notes.** "Spectrum of one sinusoid → [A₀, f₀, phase]" — a stable sinusoid needs just three
numbers per frame. "For harmonic sounds f₀ is the fundamental frequency": the harmonic model (Topic 6)
is this same model with the extra constraint `f_r = r·f₀`.

---

## Slide 5 — Computing the spectrum of a sinewave

**What it says.** Full derivation:

$$
x[n] = A\cos(2\pi k_0 n/N + \phi)
$$
$$
X[k] = A\sum_{n=0}^{N-1} w[n]\,\tfrac12\big(e^{j2\pi k_0 n/N} + e^{-j2\pi k_0 n/N}\big)e^{-j2\pi kn/N}
= \tfrac{A}{2}W[k-k_0] + \tfrac{A}{2}W[k+k_0]
$$

**Explanation.**
1. Euler: a cosine = two complex exponentials at +k₀ and −k₀, each with amplitude A/2.
2. Multiplying by `e^{j2πk₀n/N}` is a **frequency shift** (Topic 3 property): the DFT of
   `w[n]·e^{j2πk₀n/N}` is `W[k−k₀]`.
3. So the spectrum of a windowed sinusoid is **the window transform, shifted to ±k₀ and scaled by A/2**.

This is the single most important result of the topic: *every peak in the spectrum has the shape of the
window's main lobe*. It is what makes peak detection (slide 9), interpolation (11) and synthesis (18–20)
work. (The slide drops φ for clarity; with it, each term is multiplied by `e^{±jφ}`.)

**From class notes.** The sketch of a main lobe with side lobes next to "[A₀, f₀, phase]" is exactly this:
the whole lobe is determined by those three numbers.

**Videos.** Likely 5T1 *(unverified)*. Same derivation appears in the STFT video 4T1
([stft_videos.md](../../youtube_videos/stft_videos.md)).

---

## Slide 6 — Windowing shapes every sinusoidal peak

**What it says.**

$$
B_f = B_s\cdot\frac{f_s}{M},\qquad \Delta = |f_{k+1} - f_k|,\qquad M \ge B_s\cdot\frac{f_s}{\Delta}
$$

`B_s` main-lobe width in bins, `f_s` sampling rate, `M` window size, `Δ` spacing between sinusoids.
Larger M separates closer frequencies; smaller M follows faster changes; zero-padding samples the same
shape more densely. Figures: Hamming vs. Blackman-Harris main lobes.

**Explanation.** The main lobe is `B_s` bins wide *in a DFT of size M*. One bin of an M-point window is
`f_s/M` Hz, so the lobe is `B_f = B_s·f_s/M` Hz wide. Two sinusoids are resolved when their lobes don't
merge: `Δ ≥ B_f` → `M ≥ B_s·f_s/Δ`. Key point: **resolution depends on M (and the window type), not on N**.

Typical `B_s` *(own explanation, standard values)*: rectangular 2, Hann/Hamming 4, Blackman 6,
Blackman-Harris (92 dB) 8. Wider lobe ↔ lower side lobes → the window choice is a trade-off between
separating close sinusoids and seeing weak ones next to loud ones.

**From class notes.** "There is not enough frequency resolution"; "[?] is not more FFT sizes" — consistent
with this slide: increasing N does not improve resolution, only M does.

---

## Slide 7 — Choosing M is a modeling decision

**What it says.** Example: 440 Hz and 490 Hz, Hamming (B_s = 4), f_s = 44100 Hz, Δ = 50 Hz →
`M ≥ 4·44100/50 = 3528` samples. Waveform shows beating; the spectrum must show two separate peaks.

**Explanation.** 3528 samples = 80 ms. That is long: if the notes changed faster than that, the window would
smear them. This is the time–frequency compromise from Topic 4 turned into a rule: **pick the smallest M that
separates the closest pair of sinusoids you care about**. *(Own explanation:)* in sms-tools use an odd M
(e.g. 3529) so the window is symmetric around zero (zero-phase windowing), and N = next power of two ≥ M
(4096). For harmonic sounds Δ = f₀, so the lowest note sets M.

The "beating" in the waveform: two close frequencies add up to a tone at their average frequency whose
amplitude oscillates at |f₂−f₁| = 50 Hz.

---

## Slide 8 — Real musical sounds contain many overlapping peaks

**What it says.** Oboe and trumpet spectra: many peaks; the model is useful when the important structure can
be tracked as peaks.

**Explanation.** Real sounds are not two clean sinusoids: they have dozens of partials, plus noise between
them, and partials of different strength. The model works well for "tonal" sounds (stable partials) and
badly for noisy or very percussive ones.

**From class notes.** "You would listen [to] non-harmonic sounds" + "¿Cómo suena un non-harmonic sound?".
*(Own explanation:)* the sinusoidal model does **not** require harmonicity: any peaks are kept, wherever they
are. A non-harmonic sound (bell, metal plate, `bendir`) has partials that are not multiples of one f₀; it
sounds less "pitched", more metallic or inharmonic. The sinusoidal model can represent it; the harmonic model
(Topic 6) cannot.

---

## Slide 9 — Peak detection

**What it says.** Peak at bin k if `mX[k] > t`, `mX[k] > mX[k−1]` and `mX[k] > mX[k+1]`. The threshold
decides which maxima are kept; this is only a first estimate; close components may merge; phase is read at
the selected locations.

**Explanation.** A local maximum above a threshold `t` (in dB). In sms-tools this is literally one line:

```python
peaks = (mX[1:-1] > t) & (mX[1:-1] > mX[2:]) & (mX[1:-1] > mX[:-2])   # UF.peakDetection
```

Problems: (1) the true peak is usually *between* bins → slides 10–11; (2) side lobes are also local maxima.

**From class notes.** "Be careful with the appearance of side lobes if the threshold is not set properly."
Exactly: with a Hamming window side lobes are at −42 dB relative to the peak, so a threshold lower than
~(peak − 42 dB) will detect side lobes as fake sinusoids. Fixes: raise `t`, or use Blackman-Harris
(side lobes at −92 dB), at the cost of a wider main lobe.

**Videos.** 5P1 *Peak detection* *(unverified)*; notebook `examples/peak-detection-process.ipynb`.

---

## Slide 10 — Resolution problem: zero-padding helps but is not enough

**What it says.** N controls the FFT sampling grid; M controls the windowed frame; more bins do not add
information; interpolation refines the peak cheaply.

**Explanation.** Zero-padding (N > M) samples the *same* continuous spectrum more densely. It helps locate a
peak (the nearest bin is closer to the true maximum), but: it cannot separate two merged lobes (that is M's
job, slide 6), and to get e.g. 0.1 Hz accuracy at 44.1 kHz you would need N ≈ 441 000 — too expensive.
Hence: moderate zero-padding + parabolic interpolation.

---

## Slide 11 — Parabolic interpolation refines the peak tip

**What it says.** With α = mX[k_p−1], β = mX[k_p], γ = mX[k_p+1] (in dB):

$$
p(n) = a(n-\hat p)^2 + \hat b,\qquad
\hat p = \frac12\,\frac{\alpha-\gamma}{\alpha - 2\beta + \gamma},\qquad
\hat b = \beta - \frac14(\alpha-\gamma)\,\hat p
$$

Refined location `k̂ = k_p + p̂`; phase evaluated at the refined position.

**Explanation.** Fit a parabola through the three highest points and take its vertex. `p̂` is between
−0.5 and +0.5 bins. It works well because the main lobe **in dB** is close to a parabola near its top
(that's why it's done on `mX` in dB, not linear). Check: if α = γ (symmetric), p̂ = 0 and b̂ = β.

sms-tools (`UF.peakInterp`): `iploc = ploc + 0.5*(lval - rval)/(lval - 2*val + rval)`,
`ipmag = val - 0.25*(lval - rval)*(iploc - ploc)`, and the phase is linearly interpolated from `pX`
at `iploc`.

---

## Slide 12 — From one frame to sinusoidal parameters

**What it says.**

$$
\hat k = k_p + \hat p,\qquad \hat f = f_s\cdot\frac{\hat k}{N},\qquad \hat A = \hat b,\qquad \hat\varphi = \angle X[\hat k]
$$

A single frame gives isolated estimates; the model needs them connected over time.

**Explanation.** Bin → Hz conversion uses **N** (the FFT size), not M. Note `Â` is in dB. In sms-tools the window is divided by
its sum, so a sinusoid of amplitude A gives a peak of A/2 (slide 5): amplitude 1 → 20·log₁₀(0.5) ≈ −6 dB. Phase is
read at the fractional position. Output per frame: three lists `(f̂, Â, φ̂)`, one entry per peak — no
notion yet of "which peak is which" between frames.

---

## Slide 13 — From peaks to sinusoidal tracks

**What it says.** Frame peaks → frequency continuity, amplitude continuity, phase consistency → track r
through time.

**Explanation.** Tracking = deciding that peak *i* in frame *l* is the continuation of track *r* from frame
*l−1*. sms-tools (`sineTracking`) does it greedily:
1. Order current peaks by magnitude (loudest first).
2. Each peak is assigned to the closest existing track if
   `|f_peak − f_track| < freqDevOffset + freqDevSlope · f_peak`
   (the allowed deviation grows with frequency, because high partials move more in Hz during vibrato).
3. Unassigned peaks start new tracks in empty slots; tracks with no peak die (frequency 0).
4. After analysis, `cleaningSineTracks` removes tracks shorter than `minSineDur`.

---

## Slide 14 — Example: magnitude tracks on a spectrogram

**What it says.** Background = magnitude; black curves = tracks; strong ridges should support stable tracks;
gaps often signal weak or noisy regions. Example: `bendir`.

**Explanation.** The visual check: a good analysis puts long, smooth tracks on top of the bright horizontal
ridges. Many short, jittery tracks in dark areas = the threshold is too low (tracking noise or side lobes).

**Videos.** 5D1 *Sinusoidal model* (demo) *(unverified)*.

---

## Slide 15 — Phase is useful for checking sinusoidal consistency

**What it says.** Phase carries temporal alignment; abrupt phase behaviour may reveal wrong tracks; phase is
meaningful mainly near strong peaks; use it as a check, not alone.

**Explanation.** *(Own explanation:)* for a stable sinusoid of frequency f, the phase between two frames
advances predictably: `φ_l ≈ φ_{l−1} + 2π f H / f_s` (mod 2π). If a track's measured phase jumps away from
that prediction, the peak probably belongs to a different component (or to noise). Far from peaks the phase
spectrum is random-looking, so it only means something where the magnitude is strong. sms-tools tracking
uses only frequency; phase is stored and used at synthesis.

---

## Slide 16 — Synthesis: parameters become sound again

**What it says.** For each frame, generate the sinusoidal components, combine them into a time-domain frame,
overlap-add into a continuous signal.

**Explanation.** Two ways: (a) **time-domain additive synthesis** — run one oscillator per track,
interpolating A and f sample by sample; simple but cost ∝ number of sinusoids × samples. (b) **spectral
synthesis** (sms-tools) — build each frame's spectrum directly, one IFFT per frame regardless of how many
sinusoids. Slides 17–22 develop (b).

---

## Slide 17 — Synthesizing one sinusoid

**What it says.**
- Time domain: `y[n] = A[n] cos(2π f[n] n / f_s + φ₀)`.
- On-bin spectral synthesis: `y[n] = IDFT{ mY[k]·e^{j pY[k]} }` with `mY[k₀] = A`, `pY[k₀] = φ`,
  `mY[k] = 0` for k ≠ k₀.

**Explanation.** If the frequency falls exactly on a bin, one non-zero bin (plus its mirror at −k₀) gives
one perfect sinusoid after the IDFT. But real frequencies are almost never exactly on a bin → slide 18.

---

## Slide 18 — Any frequency requires a shaped spectral lobe

**What it says.**

$$
Y[k] = A_0\,|W[k-k_0]|\,e^{\,j(\angle W[k-k_0] + \varphi_0)},\qquad y[n] = \mathrm{IDFT}\{Y[k]\}
$$

Steps: compute the window transform → shift it to the target frequency → set amplitude and phase → IDFT.
Figures: magnitude and phase of shifted Hamming and Blackman-Harris spectra.

**Explanation.** This is slide 5 run backwards: analysis showed that a windowed sinusoid's spectrum is the
shifted window transform; so to *create* one, draw that shifted window transform in the spectrum. Here k₀
can be fractional (e.g. 23.4 bins). After the IDFT you get a sinusoid already multiplied by the window.

---

## Slide 19 — Main-lobe synthesis

**What it says.** Most energy is in a few bins around the peak. Hamming: first side lobe ≈ −42 dB;
Blackman-Harris: much lower; wider main lobe costs more bins; good synthesis often uses only the main lobe.

**Explanation.** Drawing the whole window spectrum for every sinusoid would be expensive. With
Blackman-Harris the side lobes are at −92 dB (inaudible), so you only draw the **main lobe**: in sms-tools
`UF.genBhLobe` computes 9 bins (±4 around the peak) and `genSpecSines` adds them at each frequency.
That's why sms-tools always synthesises with Blackman-Harris, whatever window you used for analysis.

---

## Slide 20 — Additive synthesis in the spectrum

**What it says.**

$$
Y[k] = \sum_r A_r\,|W[k-k_r]|\,e^{\,j(\angle W[k-k_r] + \varphi_r)}
$$

Each sinusoid contributes one shifted lobe with its own amplitude and phase; sum → IFFT.

**Explanation.** Linearity of the DFT: sum of sinusoids in time = sum of their lobes in frequency. One frame
with 50 sinusoids = 50 × 9 bins written into one spectrum + one IFFT of size Ns = 512. Much cheaper than 50
oscillators × 512 samples.

**Videos.** 5P2 *Sinusoidal synthesis* *(unverified)*; notebook `examples/sinusoidal-synthesis-examples.ipynb`.

---

## Slide 21 — Sinusoidal model system

**What it says.** Analysis: x[n] → STFT → peaks → interpolation → tracking → tracks (A, f, φ).
Synthesis: tracks → spectral lobes → IFFT → overlap-add → y[n]. The tracks are the compact model.

**Explanation.** This is `sineModel.py`:

```python
tfreq, tmag, tphase = SM.sineModelAnal(x, fs, w, N, H, t, maxnSines, minSineDur, freqDevOffset, freqDevSlope)
y = SM.sineModelSynth(tfreq, tmag, tphase, Ns=512, H=128, fs=fs)
```

`tfreq`, `tmag`, `tphase` are matrices (frames × maxnSines); 0 = no sinusoid in that slot. Note analysis and
synthesis use **different** sizes: analysis M, N chosen for resolution; synthesis always Ns = 512, H = 128.
Transformations (Topic 8) happen between the two calls, by editing the tracks.

**Videos.** 5P3 *SineModel* *(unverified)*.

---

## Slide 22 — Synthesis window and overlap-add

**What it says.** IFFT gives a frame windowed by Blackman-Harris (Ns = 512); "undo analysis window and apply
synthesis window": `yw = y · triangular / Blackman-Harris` (size Ns/2 = 256); overlap and add; hop H sets
the overlap.

**Explanation.** The IFFT output is a sinusoid shaped by a Blackman-Harris window (because we drew BH lobes).
BH windows don't add up to a constant when overlapped, so sms-tools divides by BH and multiplies by a
**triangular window of length 2H = 256**. Triangles of length 2H with hop H add to exactly 1 → no amplitude
ripple, and the triangle cross-fades linearly between frames (smooth A and f transitions).
*(Own note:)* the slide says "25% overlap example"; with Ns = 512 and H = 128 the hop is 25% of the frame,
i.e. 75% overlap of the 512 frames, and 50% overlap of the 256-sample triangles.

---

## Slide 23 — Example: analysis/synthesis result

**What it says.** Check: are important tracks continuous? are attacks and decays preserved? does it sound
close enough? which components are missing or unstable?

**Explanation.** The test is both visual and by listening. Typical failures: attacks sound soft/smeared
(window too long, sinusoids can't represent transients); "watery"/"birdie" artefacts (many short spurious
tracks → raise `t` or `minSineDur`); missing breathiness (noise is not sinusoidal → residual, Topic 7).

**Videos.** 5D1, 5D2 *Analyzing a sound* *(unverified)*.

---

## Slides 24–25 — Example notebooks / how to read the outputs

**What they say.** `examples/peak-detection-process.ipynb` (peaks and interpolation),
`examples/sine-model-examples.ipynb` (full workflow, tracks, parameters),
`examples/sinusoidal-synthesis-examples.ipynb` (synthesis from parameters). Four questions: are the right
peaks detected? are tracks connected without jumps? do M, N, H, t and window explain the result? does the
output match visually and by listening?

---

## Slide 26 — Exercise 5: Sinusoidal model

**What it says.** Open `exercises/E5-Sinusoidal-model.ipynb`. Five parts: (1) estimate a single sinusoid,
minimise frequency error; (2) track a two-component chirp, choose M carefully; (3) separate components with a
large amplitude range — window + threshold; (4) use phase to check track stability; (5) resynthesise a
multiSines analysis, inspect and listen. Uses `sineModelAnal()` / `sineModelSynth()`.

**Explanation.** Each part maps to slides: 1 → 10–12, 2 → 6–7 + 13, 3 → 6 + 9 + 19, 4 → 15, 5 → 21–23.

**From class notes.** "[Chirps?] can be approximated [as] linear systems, straight lines" — relevant to part 2:
a chirp's frequency changes linearly, so over one short frame it looks like a straight line and the model
treats it as a slowly moving sinusoid, as long as M is short enough.

---

## Slide 27 — Lab 5: change parameters deliberately

| Parameter | Meaning | What to observe |
|---|---|---|
| `M` | window length | frequency separation vs. time precision |
| `N` | FFT size | spectral sampling, interpolation support |
| `t` | threshold (dB) | reject weak/noisy peaks vs. keep faint sinusoids |
| `maxnSines` | track limit | max sinusoids per frame |
| `minSineDur` | cleaning | remove short spurious tracks |
| `freqDevOffset` / `freqDevSlope` | tracking tolerance | allow frequency motion between frames |

**Explanation.** Change **one at a time** and look + listen. Defaults in
[interface-models/sineModel_function.py](../../interface-models/sineModel_function.py): `hamming`, M = 2001,
N = 2048, t = −80, minSineDur = 0.02, maxnSines = 150, freqDevOffset = 10, freqDevSlope = 0.001.

---

## Slide 28 — What to remember from Topic 5

Model (time-varying sinusoids) · Peaks (f, A, φ from spectra) · Windows (M and shape control leakage and
resolution) · Tracks (link peaks into components) · Synthesis (main lobes, IFFT, synthesis window, OLA) ·
Validation (inspect magnitude/phase, tracks, waveform, listen).

**Slide 29 (references).** sms-tools repository and notebooks; Serra & Smith (1990), *Spectral modeling
synthesis*.

---

## Key takeaways

1. A windowed sinusoid's spectrum = the window transform shifted to its frequency, scaled by A/2 —
   everything in this topic builds on that.
2. Resolution is set by **M and the window**: `M ≥ B_s·f_s/Δ`. N (zero-padding) only samples more densely.
3. Peaks = local maxima above `t`; a too-low threshold picks up side lobes.
4. Parabolic interpolation in dB gives fractional-bin frequency and amplitude cheaply.
5. Tracking links peaks frame to frame with a frequency-dependent tolerance; short tracks are removed.
6. Synthesis is done in the spectrum: one Blackman-Harris main lobe (9 bins) per sinusoid → IFFT.
7. Overlap-add with a triangular synthesis window (length 2H) gives smooth, ripple-free output.
8. The model loses non-sinusoidal parts (noise, transients) → harmonic model (6) and residual (7) next.
