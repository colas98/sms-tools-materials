# Harmonic model – Xavier Serra's video lectures (week 6)

Week-6 videos on the Harmonic Model and F0 detection from the channel
[Xavier Serra (@XavierSerraC)](https://www.youtube.com/@XavierSerraC/videos)
(course *Audio Signal Processing for Music Applications*).
Matches the slides [06-Harmonic-model.pdf](../lectures/06-Harmonic-model.pdf) and the class notes
[harmonic_notes.md](../class_notes/harmonic_notes.md).

> **Status: video list only, no content summary yet.** The captions could not be downloaded automatically
> (YouTube returns an empty response from the caption endpoint without a browser token). Titles, links and
> durations below are verified from YouTube search results. The "Likely slides" column is **inferred from
> the title only and not checked against the video**. To complete this file, paste each video's transcript
> (YouTube → "⋯" → *Show transcript*) and it will be summarised in the format of
> [stft_videos.md](stft_videos.md). The same lectures (with transcripts, after a free enrolment) are also on
> [Coursera – Audio Signal Processing for Music Applications](https://www.coursera.org/learn/audio-signal-processing).

| Code | Video | Length | Type | Likely slides *(unverified)* |
|---|---|---|---|---|
| 6T1 | [Harmonic model](https://www.youtube.com/watch?v=GXA3oyT8eu4) | 13:46 | Theory | 3–12: model equation, partials vs harmonics, mono/polyphonic, harmonic detection, system |
| 6T2 | [F0 detection](https://www.youtube.com/watch?v=0_fTFNzmOWM) | 20:25 | Theory | 13–26: F0 vs pitch, autocorrelation, YIN, spectral F0, TWM, polyphonic F0 |
| 6P1 | [F0 detection](https://www.youtube.com/watch?v=qubxTbeSBNM) | 16:54 | Programming | 21–24 · `UF.f0Twm`, `HM.f0Detection` |
| 6P2 | [harmonicModel](https://www.youtube.com/watch?v=SjtaV1IQKPc) | 14:27 | Programming | 27 · `harmonicModel.py` (`harmonicModelAnal`, `harmonicDetection`) |
| 6D1 | [Pitch detection](https://www.youtube.com/watch?v=UrA9SoUKg2w) | 14:44 | Demo | 14, 16, 18, 24 · F0 of real sounds (oboe, piano, voice) |
| 6D2 | [Harmonic model](https://www.youtube.com/watch?v=PEERoIMkF-0) | 25:02 | Demo | 12, 28–29 · harmonic analysis/synthesis of real sounds |

Related code (installed `smstools` package): `smstools/models/harmonicModel.py`,
`smstools/models/utilFunctions.py`; GUI wrapper in
[interface-models/harmonicModel_function.py](../interface-models/harmonicModel_function.py).
Example notebooks: [f0-detection.ipynb](../examples/f0-detection.ipynb),
[harmonic-model-examples.ipynb](../examples/harmonic-model-examples.ipynb).
