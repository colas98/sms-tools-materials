# Topic 6: Harmonic Model – slide-by-slide explanation

Slides: [06-Harmonic-model.pdf](../06-Harmonic-model.pdf) ·
Class notes: [harmonic_notes.md](../../class_notes/harmonic_notes.md) ·
Videos: [harmonic_videos.md](../../youtube_videos/harmonic_videos.md) ·
Lab: [E6-Harmonic-model.ipynb](../../exercises/E6-Harmonic-model.ipynb)

Sources, in order of priority: the slides → class notes → sms-tools code → own explanation.
Text marked *(own explanation)* does not come from the course materials. Video mappings are inferred
from the video titles only (captions not available yet) and are marked *(unverified)*.

**Slides 1–2 (title, roadmap).** Harmonic model → partials & harmonics → harmonic detection →
F0 detection → example notebooks → Exercise 6.

---

## Slide 3 — From sinusoidal to harmonic representation

**What it says.** Pipeline: STFT frames → sinusoidal model (peaks + tracks) → harmonic model (tracks
constrained by F0) → analysis/synthesis. Use it only when the source is periodic or pseudo-periodic.

**Explanation.** Same machinery as Topic 5, plus one constraint: all the sinusoids share a common F0.
Each step is more compact and more "meaningful" than the previous one, but assumes more about the sound.

**From the videos.** 6T1 *(unverified)*.

---

## Slide 4 — Harmonic model

**What it says.**

$$
y_h[n] = \sum_{r=1}^{R} A_r[n]\cos\!\big(2\pi\, r\, f_0[n]\, n + \varphi_r[n]\big)
$$

The only difference with the sinusoidal model: $f_r[n] = r\cdot f_0[n]$. Valid for single-source, periodic
sounds (instruments, voice with stable pitch); poor for strongly inharmonic sounds and dense polyphony.
*More restrictive, but more interpretable.*

**Explanation.** In Topic 5 each track had its own free frequency. Here one number per frame, $f_0[n]$,
fixes all of them. That makes transformations easy *(own explanation)*: transposing = scaling $f_0$, and
every harmonic follows.

**From class notes.** $f_r[n] = r\cdot f_0[n]$; applied to single-source / source-separated signals;
"potential for transformations / interpretation".

---

## Slide 5 — Harmonic spectrum as shifted window spectra

**What it says.**

$$
Y_h[k] = \sum_r A_r\, W[k - r f_0]
$$

Each harmonic is a copy of the window transform $W$ centred at $r f_0$ (in bins), scaled by $A_r$.

**Explanation.** Same idea as the sinusoid spectrum in Topic 3/4 (a sinusoid = shifted window lobe), but
the lobes now sit on a regular grid $f_0, 2f_0, 3f_0,\dots$. This is why peaks must "align with an integer
series" and why knowing F0 first makes it easy to pick the harmonics. Resynthesis needs only $(A_r, r f_0)$.

---

## Slide 6 — Inharmonic vs harmonic sinusoidal tracks

**What it says.** Sinusoidal tracks of a vibraphone are not locked to a harmonic series; a voice shows
many parallel harmonic ridges. The model choice depends on the source, not the algorithm.

**From class notes.** Vibraphone → not harmonic (metal bar); tuned **tubes** under the bars reinforce the
fundamental. Piano harmonics are **stretched**; longer strings (grand piano) are closer to harmonic.
Wind instruments oscillate harmonically; cymbals are among the most inharmonic.

**Explanation** *(own explanation)*. A stiff bar or string has partials at non-integer ratios; an air column
or flexible string is close to integer ratios. Stiffness = inharmonicity.

---

## Slide 7 — Sinusoids, partials, and harmonics

**What it says.**
- **Sinusoid**: mathematical function (one frequency, amplitude, phase).
- **Partial**: a stable component of a sound, modelled by a slowly varying sinusoid.
- **Harmonic**: a partial whose frequency is close to an integer multiple of F0.

**Explanation.** Every harmonic is a partial, but not every partial is a harmonic (vibraphone, piano's
stretched partials). "Close to" matters: real harmonics deviate slightly, which is why detection uses a
tolerance (slide 10).

---

## Slide 8 — Phase spectra also reveal differences between components

**What it says.** Find candidates in the magnitude spectrum; use phase near significant peaks; compare
related peaks; don't assume every stable peak is harmonic.

**Explanation** *(own explanation)*. Around a true sinusoidal peak the phase is flat-ish across the main lobe
(for a zero-phase window); in noise it jumps randomly. Phase is a consistency check, not a detector.

**From class notes.** "Phases of piano are crazy" (noted under the piano-tuner discussion).

---

## Slide 9 — Polyphonic and monophonic signals

**What it says.** Monophonic = one source to model; polyphonic = several overlapping harmonic series
that can overlap or mask each other, making F0 detection much harder.

**Explanation.** The model has *one* $f_0$ per frame, so it describes a single voice. Two notes a fifth apart
share harmonics (3·f₁ = 2·f₂), so peaks can't be assigned uniquely *(own explanation)*.

---

## Slide 10 — Harmonic detection after F0 detection

**What it says.** Peak $p$ is harmonic $h$ if $|f_p - h f_0| <$ threshold (Hz or cents), $h = 1..H$.
Common mistakes: assuming the largest peak is F0, accepting every peak, not checking deviation,
forgetting noisy peaks break tracks.

**sms-tools.** `harmonicDetection()` in `harmonicModel.py`: for each $h$ it takes the closest peak and
accepts it if the deviation from $h f_0$ **or** from the previous frame's harmonic is below

$$
\text{threshold} = \frac{f_0}{3} + \text{harmDevSlope}\cdot f_p
$$

So the tolerance grows with frequency — high harmonics are allowed to drift more (inharmonicity).

---

## Slide 11 — Harmonic model system

**What it says.** Analysis: $x[n]$ → window → FFT → peak detection → **F0 detection** → **harmonic
detection** → $(A_r, f_r, \varphi_r)$. Synthesis: sine spectral synthesis → IFFT → overlap-add → $y[n]$.

**Explanation.** Identical to the sinusoidal system of Topic 5 except the two boxes in bold, which replace
free peak tracking. Synthesis is unchanged (`sineModelSynth` works on harmonic tracks too).

---

## Slide 12 — Harmonic analysis/synthesis of a voice sound

**What it says.** Plots: input waveform, harmonic frequencies, resynthesized waveform. Gaps or wrong
tracks become audible; listening confirms the model is adequate.
See `examples/harmonic-model-examples.ipynb`.

**From the videos.** 6D2 *(unverified)*.

---

## Slide 13 — Fundamental frequency and pitch

**What it says.** **F0**: physical, in Hz, estimated from waveform or spectrum. **Pitch**: perceptual
("high/low"), related to F0 but affected by timbre and context.

**From class notes.** f0 ≠ pitch: a louder sound gives the **impression of higher pitch**.

**From the videos.** 6T2 *(unverified)*.

---

## Slide 14 — Sound examples for F0 detection

**What it says.** `oboe-A4.wav` (clear harmonic structure), `piano.wav` (time-domain detection less
clean), `carnatic.wav` (polyphonic mixture with a prominent voice). Difficulty grows left to right.

---

## Slide 15 — F0 detection with autocorrelation

**What it says.**

$$
r_x[\ell] = \sum_n x[n]\,x[n-\ell], \qquad \ell = 0,1,\dots,N-1
$$

Compare the signal with delayed copies; the lag of the first big peak (after $\ell=0$) is the period $T_0$;
$F_0 = f_s / \ell_{\text{peak}}$. Works best for monophonic, stable, clearly periodic sounds.

**Explanation.** If the signal repeats every $T_0$ samples, $x[n]$ and $x[n-T_0]$ are almost equal, so
their product sums to a large value. Estimates a *period*, not a spectral peak — so it works even when the
fundamental itself is missing *(own explanation)*.

**From class notes.** Tuners are based on **periodicity** (e.g. zero-crossing rate).

---

## Slide 16 — Autocorrelation examples: oboe vs piano

**What it says.** Oboe: clear periodicity, clean peaks. Piano: more ambiguous.

**From class notes.** Piano tuning is done by inspecting the **frequency domain** (sympathetic resonances);
the piano waveform is irregular and decays towards a clean sinusoid.

---

## Slide 17 — YIN algorithm: difference function

**What it says.**

$$
d(\tau) = \sum_j \big(x_j - x_{j+\tau}\big)^2, \qquad \tau = 0,1,\dots,N-1
$$

Small values = signal matches its delayed version; **minima** are candidate periods. Designed for
monophonic F0 tracking; still needs post-processing to avoid jumps.

**Explanation.** Expanding the square: $d(\tau) = r(0) + r_\tau(0) - 2r(\tau)$, so it is autocorrelation
"turned upside down" plus energy terms *(own explanation)*. YIN then normalises $d(\tau)$ to avoid picking
$\tau = 0$ (de Cheveigné & Kawahara, 2002).

**From class notes.** "YIN algorithm → version of autocorrelation."

---

## Slide 18 — YIN output over a vocal spectrogram

**What it says.** Check: does F0 follow the voice contour? Octave jumps? Are unvoiced/noisy regions
ignored? Does the curve align with the lowest harmonic ridge?

**sms-tools.** `examples/f0-detection.ipynb`.

---

## Slide 19 — F0 detection from spectral peaks

**What it says.** In the spectrum, F0 is the common spacing that best explains the peaks. The lowest
strong peak is not always F0; F0 can be missing or weak → F0 detection is **pattern matching**.

**From class notes.** **Missing fundamental**: low piano notes barely produce the fundamental, but the
ear-brain reconstructs it from the harmonics' spacing.

---

## Slide 20 — F0 as a common divisor in the spectrum

**What it says.** An F0 predicts a regular grid $F_0, 2F_0, 3F_0,\dots$; measured peaks should fall near
it. Extra peaks may be noise/another source; missing harmonics do not invalidate the F0.

**Explanation.** F0 ≈ greatest common divisor of the peak frequencies. E.g. peaks at 200, 300, 500 Hz →
F0 = 100 Hz even with no peak at 100 Hz.

---

## Slide 21 — Two-way mismatch as pattern matching

**What it says.** For each candidate F0 compare in two directions:
1. **predicted → measured**: is each predicted harmonic near a real peak?
2. **measured → predicted**: is each real peak explained by the series?

**Explanation.** One direction alone fails: p→m alone favours high F0s (few predicted harmonics, all
matched → octave-up errors); m→p alone favours very low F0s (a dense grid explains everything → octave-down
errors). Combining both penalises both errors *(own explanation, following Maher & Beauchamp 1994)*.

---

## Slide 22 — TWM error calculation

**What it says.**

$$
\text{Err}_{total} = \frac{\text{Err}_{p\to m}}{N} + \rho\,\frac{\text{Err}_{m\to p}}{K}
$$

$N$ = predicted harmonics, $K$ = measured peaks, $\rho$ = weight. Example with peaks at
200, 300, 500, 600, 700, 800 Hz: candidates 50 Hz → 7.49, **100 Hz → 3.83**, 200 Hz → 4.2. 100 Hz wins.

**Explanation.** 200 Hz matches 200, 600, 800 well but leaves 300, 500, 700 unexplained (high m→p error).
50 Hz explains everything but predicts many harmonics with no peak (high p→m error). 100 Hz balances both.

**sms-tools.** `UF.f0Twm()` → `TWM_p()` in `utilFunctions.py` (uses $\rho = 0.33$ and magnitude-weighted
errors). Numbers in the table are copied from the slide, not recomputed.

**From the videos.** 6P1 *(unverified)*.

---

## Slide 23 — TWM error curves for the oboe sound

**What it says.** Error curves over all candidate F0s; the minimum of the total error is the estimate.
Wrong octaves can create local minima.

---

## Slide 24 — F0 trajectory for a piano phrase

**What it says.** Frame-level F0 → time-varying pitch contour. Failure cases: octave errors, noisy/missing
estimates, unstable attacks, competing F0s in mixtures.

**sms-tools.** `f0Detection()` adds stability: `f0stable` is kept only if the new estimate differs by less
than `f0et`; it is passed to `f0Twm` to favour continuity.

---

## Slide 25 — F0 in polyphonic signals

**What it says.** Several harmonic series at once; sources overlap, some are inharmonic/percussive,
harmonics coincide, a melody can mask weaker instruments; F0 errors propagate to the whole model.

---

## Slide 26 — Prominent pitch in polyphonic signals

**What it says.** Practical algorithms estimate the **dominant melodic pitch** (melody extraction), not
all F0s. Not a full transcription; shows F0 detection is still an open problem
(Salamon & Gómez, 2012).

---

## Slide 27 — `harmonicModelAnal()`: waveform → harmonic tracks

**What it says.**
1. **Window + DFT**: pad, normalise $w$, `mX, pX = dftAnal(x1, w, N)` per frame.
2. **Spectral peaks** above `t` dB, interpolated; `ipfreq = fs · iploc / N`.
3. **F0 (TWM)**: `f0Twm()` with `minf0`, `maxf0`, `f0et` and the stable previous F0.
4. **Select harmonics**: `harmonicDetection()` near $r F_0$ or previous-frame harmonics; up to `nH`
   below Nyquist.

Then `cleaningSineTracks()` removes tracks shorter than `minSineDur`. Returns `xhfreq` (Hz), `xhmag`
(dB), `xhphase` (rad), each `nFrames × nH`.

**sms-tools.** `smstools/models/harmonicModel.py`. **From the videos.** 6P2 *(unverified)*.

---

## Slides 28–29 — Example notebooks / How to read the outputs

**What it says.** Notebooks: `examples/f0-detection.ipynb`, `examples/harmonic-model-examples.ipynb`,
`harmonicModel_function.py` (GUI). Check in order: **F0** explains the peaks? **Harmonics** near integer
multiples? **Tracks** stable? **Resynthesis** keeps pitch, amplitude evolution and timbre?

---

## Slides 30–31 — Exercise 6

**What it says.** Lab `exercises/E6-Harmonic-model.ipynb`: `f0Detection()` → `harmonicModelAnal()` →
`harmonicModelSynth()`; steps F0 → peaks → tracks → synth → check. (Note: the installed `harmonicModel.py`
has no `harmonicModelSynth()`; synthesis is done with `SM.sineModelSynth(hfreq, hmag, hphase, Ns, H, fs)`.)
Parameters:

| parameter | meaning | what to observe |
|---|---|---|
| `minf0` / `maxf0` | F0 search range | wrong range → octave errors or no estimate |
| `f0et` | F0 error threshold | too strict rejects frames; too loose accepts wrong F0 |
| `harmDevSlope` | harmonic deviation tolerance | larger keeps unstable harmonics but may add wrong peaks |
| `t` | peak threshold | controls weak/noisy peaks before selection |
| `maxnHarm` | harmonic limit | max harmonics per frame |
| `minSineDur` | track cleaning | removes short, spurious tracks |

**Tip** *(own explanation)*. Set `minf0`/`maxf0` tightly around the expected note range first — it is the
parameter that fixes most octave errors.

---

**Slides 32–33 (summary, references).** Summary below. References: YIN (de Cheveigné & Kawahara 2002),
TWM (Maher & Beauchamp 1994), melody extraction (Salamon & Gómez 2012).

## Key takeaways

1. Harmonic model = sinusoidal model + constraint $f_r[n] = r\, f_0[n]$; one F0 per frame drives all partials.
2. Only valid for single, (pseudo-)periodic sources; inharmonic (vibraphone, bells, cymbals) or polyphonic
   sounds break it.
3. Harmonic ⊂ partial ⊂ sinusoid; real harmonics deviate slightly, so detection uses a frequency-dependent
   tolerance (`harmDevSlope`).
4. F0 (Hz, physical) ≠ pitch (perceptual); the fundamental can be missing and still be heard.
5. Time-domain F0: autocorrelation (maxima) and YIN (minima of a difference function) — good for clean
   monophonic sounds.
6. Spectral F0: pattern matching; TWM combines predicted→measured and measured→predicted errors to avoid
   octave errors.
7. Pipeline in sms-tools: `dftAnal` → peaks → `f0Twm` → `harmonicDetection` → `cleaningSineTracks` →
   `sineModelSynth`.
8. Always validate by looking (F0, tracks, spectrum) **and** listening.
