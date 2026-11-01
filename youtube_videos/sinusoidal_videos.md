# Sinusoidal model – Xavier Serra's video lectures (week 5)

Week-5 videos on the Sinusoidal Model from the channel
[Xavier Serra (@XavierSerraC)](https://www.youtube.com/@XavierSerraC/videos)
(course *Audio Signal Processing for Music Applications*).
Matches the slides [05-Sinusoidal-model.pdf](../lectures/05-Sinusoidal-model.pdf) and the class notes
[sinusoidal_notes.md](../class_notes/sinusoidal_notes.md).

> **Status: video list only, no content summary yet.** The captions could not be downloaded automatically
> (YouTube now blocks the caption endpoint without a browser token). Titles, links and durations below are
> verified from YouTube. The "Likely slides" column is **inferred from the title only and not checked
> against the video**. To complete this file, paste each video's transcript
> (YouTube → "⋯" → *Show transcript*) and it will be summarised in the format of
> [stft_videos.md](stft_videos.md).

| Code | Video | Length | Type | Likely slides *(unverified)* |
|---|---|---|---|---|
| 5T1 | [Sinusoidal model 1](https://www.youtube.com/watch?v=TWtMS2gzR6A) | 15:19 | Theory | 3–8: model idea, spectrum of a windowed sinusoid, choosing M |
| 5T2 | [Sinusoidal model 2](https://www.youtube.com/watch?v=DwtaZH9uOiw) | 13:30 | Theory | 9–15: peak detection, interpolation, tracking |
| 5T3 | [Sinusoidal model 3](https://www.youtube.com/watch?v=ZxcZjs7SYj0) | 17:04 | Theory | 16–23: synthesis, main lobes, overlap-add, full system |
| 5P1 | [Peak detection](https://www.youtube.com/watch?v=QC9_oXZMndg) | 14:50 | Programming | 9–12 · `UF.peakDetection`, `UF.peakInterp` |
| 5P2 | [Sinusoidal synthesis](https://www.youtube.com/watch?v=OgrFgvCNjy8) | 12:48 | Programming | 17–20 · `UF.genSpecSines`, `UF.genBhLobe` |
| 5P3 | [SineModel](https://www.youtube.com/watch?v=4GMF_GMFraU) | 16:07 | Programming | 21–22 · `sineModel.py` (`sineModelAnal`, `sineModelSynth`) |
| 5D1 | [Sinusoidal model](https://www.youtube.com/watch?v=vNBrZZ8tmFw) | 13:11 | Demo | 14, 23 · analysis/synthesis of real sounds |
| 5D2 | [Analyzing a sound](https://www.youtube.com/watch?v=7P2gqEXAJRc) | 12:41 | Demo | 23, 27 · choosing parameters for one sound |

Related code (installed `smstools` package): `smstools/models/sineModel.py`,
`smstools/models/utilFunctions.py`; GUI wrapper in
[interface-models/sineModel_function.py](../interface-models/sineModel_function.py).
