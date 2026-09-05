# Changelog

All notable changes to AstraGL Player are documented here.

## [1.2.0] — 2026-09-05


### Playback
- Hardware decoding via WebCodecs (HEVC, AV1, VP9), with an FFmpeg fallback for
  legacy and incompatible formats.
- **Display Sync** — refresh-rate-aware frame interpolation, with a ½ Rate option
  for high-refresh monitors. Motion is compensated with optical flow, so the
  in-between frames follow the movement instead of cross-fading through it.
- **Resume playback** — a film reopens exactly where you left it. The position is
  saved every five seconds, so an unexpected shutdown costs a few seconds rather
  than the whole film. Films under 30 seconds in are not remembered, and one
  watched to the end never resumes in the credits.
- **File associations** — AstraGL can register itself for 16 video formats and open
  a film on double-click, with a switch in the interface to register or unregister.
  Registration is per-user, needs no administrator rights, and repairs itself if
  the portable kit is moved to another folder.

### Picture
- GPU-accelerated real-time processing: adaptive sharpening, deband, detail
  enhance, film grain, deblock, micro contrast for older standard-definition
  sources. Filters are compiled specialised to the options actually switched on,
  so unused work costs nothing.
- **HDR10, HLG and Dolby Vision** — detection, tone mapping and passthrough, with
  a shadow gamma control for dark scenes.
- **GPU Upscale** for lower-resolution sources on larger displays, an optional
  **Super Upscale 8K** pass, and a **4K downscale** mode for conversion work.
- **Motion-Compensated TAA** stabilises grain and noise without leaving trails on
  movement, and **Pan Shutter** removes judder from 24fps camera pans.
- **A/B comparison on the `A` key** — splits the screen between the unprocessed
  source and the processed picture, so every setting can be judged directly.
- **Diagnostics overlay on the `C` key** — shows what each stage is actually doing.

### Sound
- Audio output as Auto, Stereo or Surround. Auto keeps 5.1 and 7.1 when the
  Windows device reports enough channels, and downmixes safely when it does not.
- Loudness normalisation to EBU R128, voice clarity boost, bass shelf, stereo
  widening, and audio-track selection.

### Subtitles and streaming
- Embedded and external subtitles, loaded quickly even deep into large 4K files.
- **AstraGL Cast** — LAN streaming to any device through VLC or any player that
  supports "Open Network Stream".

### Licensing
- 14-day trial, fully functional, with no watermark and no session limit.
  Afterwards the player continues as a demo — 5 minutes of playback per launch,
  with a watermark — so your files never become unplayable. A one-time purchase
  removes every limit permanently.