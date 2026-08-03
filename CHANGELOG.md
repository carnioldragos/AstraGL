# Changelog

All notable changes to AstraGL Player are documented here.

## [1.1.0] — 2026-08-02

### Added
- **Perceptual Depth** — a real-time depth presentation mode that works purely
  through light and colour, with no AI, no depth map, no optical flow and no
  geometric warp of any kind. It combines adaptive lens separation, surface-volume
  modelling, edge-safe micro depth-of-field, atmospheric bloom, and a
  luminance-preserving variant of Chromadepth: nearer planes are pushed slightly
  warmer, distant planes slightly cooler, exploiting the eye's own chromatic
  aberration to suggest depth. Because no pixel is ever displaced, the mode
  cannot produce the tearing, doubling or smearing that displacement-based 2.5D
  effects suffer from, and it behaves identically whether the film is playing or
  paused. It also preserves the native source framebuffer and never reduces
  resolution to the player-window size.
- **Super Upscale 8K** — an experimental GPU option that renders the final
  reconstruction pass at up to 7680×4320 while preserving the source aspect
  ratio. The decode and main filter chain remain at source resolution, keeping
  the cost substantially lower than processing the entire pipeline at 8K.
  Driver texture/renderbuffer limits are checked automatically, and the option
  is disabled safely on unsupported hardware. Mutually exclusive with 4K
  Conversion Downscale, so the 8K pass always receives the native source.
- **Instant A/B comparison on the `A` key** — cycles through unprocessed
  original, vertical A/B split, and normal playback. Both sides use identical
  framing, filters, color treatment, sharpen/upscale, and overscan. With
  Perceptual Depth active, the split isolates that stage alone: both halves are
  processed identically and only the right side receives the depth treatment.
  With it disabled, the split retains the original-versus-processed comparison.
- **Diagnostics overlay on the `C` key** — cycles through hidden, essential,
  and full. The essential view reports source/decoded/render resolution and the
  active Native / GPU Upscale / Perceptual Depth / Super 8K path, bit depth and
  color pipeline, frame pacing against the display's VSync budget, audio route
  and buffer health, and player/system memory. A second press adds the internal
  view: decoder and demuxer queues, per-stage GPU timings, memory breakdown, and
  detailed audio event counters. If a heard gap leaves all counters unchanged
  while the buffer remains healthy, the interruption occurred downstream in
  Windows, the audio driver, or Bluetooth.
- **Audio Output selector** — Auto, Stereo, and Surround output modes. Auto
  preserves multichannel output when the active device supports it; Stereo
  performs a dialogue-safe downmix; Surround preserves 5.1/7.1 layouts and
  converts browser-incompatible formats such as DTS to multichannel AAC when
  required. Switching between modes that resolve to the same channel layout
  takes effect immediately, without rebuilding the FFmpeg stream.
- **4K Conversion Downscale** — an explicit performance option for converting
  all 4K SDR/HDR content to 1440p through the hardware path. With the option
  disabled, compatible content stays native/direct; the software fallback
  remains capped at 1080p for reliable playback.
- **Micro Contrast for legacy SD sources** — restores restrained local tonal
  separation on older, low-contrast material without globally crushing blacks
  or clipping highlights.
- **3D Enhance** — a new stereo widening control (Audio settings), using
  Mid-Side processing to boost the difference between left/right channels
  for a wider, more immersive soundstage. No artificial delays or phase
  tricks. Defaults to 130%; 100% is untouched/natural, 0% is mono. Includes
  automatic center-channel compensation, so dialogue (which sits dead-center
  in the mix) stays forward and clear instead of receding as the sides widen.
- **Bass Boost** — a new low-frequency shelf control (Audio settings), 0-8 dB.
  Defaults to 0.5 dB (essentially natural, subtle from the start).

### Changed
- Replaced the permanent watermark and 15-minute session restriction with a
  complete 14-day calendar trial. Every player feature remains available
  throughout the trial, with no watermark and no playback-duration limit. After
  the 14 days, the player keeps working as a demo — 5 minutes of playback per
  application launch, with a watermark — until a license is activated. The first trial registration is now
  anchored by `license.carniol.xyz` using only a locally hashed, pseudonymous
  Windows installation identifier. The VPS returns an Ed25519-signed token with
  the original expiry, so deleting local state or reinstalling the player cannot
  restart the 14 days. Internet is required once; the signed token is then
  verified offline and stored redundantly through encrypted AppData files and
  the current-user Windows registry, with clock-rollback protection. The About
  panel shows `Unregistered` during the trial and `Registered to: <customer
  name>` after activation, using the signed name embedded in the permanent
  license without exposing the customer's email.
- Recalibrated UHD SDR direct play to approximately 1.80 effective sharpening,
  0.10 denoise, 0.01 spatial smoothing, and a maximum of 0.10 deband. SD
  material now defaults to 0.30 deband instead of 0.60 to avoid a washed-out
  appearance.
- Sharpen strength now defaults and resets to 2.00 in Auto, RCAS, and Adaptive
  modes; Auto still selects the appropriate algorithm and effective
  source-aware strength.
- Recalibrated the master volume response and loudness processing for a more
  natural perceived level around 50%, while retaining EBU R128 normalization
  and dialogue enhancement.

### Fixed
- **Fixed audio lagging roughly half a second behind the picture after seeking**
  (on the route where video plays natively and audio is re-encoded to AAC by
  FFmpeg). Jumping to a new position moved the picture instantly but let it keep
  playing while the new audio stream was still starting up — so the video ran
  ahead by exactly the audio start-up time (measured at ~0.64 s). The only
  correction available was a 3% audio speed-up, which needed about 20 seconds to
  claw that back. Playback now waits for the new audio stream and re-aligns the
  picture to it before resuming, so sound and image start together.
- **Fixed WebCodecs audio stutter and temporary A/V offset after seeking.**
  Audio now restarts with a protected preroll and clean synchronization state,
  avoiding both the initial stumble and the slow drift back into sync.
- **Fixed occasional pitch-bending ("doppler") audio right after seeking**,
  most noticeable on WebCodecs playback: two independent A/V sync mechanisms
  (an offset corrector and a playback-speed corrector) fed into each other's
  drift measurement, so the speed corrector could react to the offset
  corrector's own recent adjustment instead of a real drift — applying an
  audible pitch-bend to fix a discrepancy that wasn't really there. The
  speed corrector now waits for a clean reading before acting.
- **Fixed an occasional loud volume spike after seeking into a quiet scene**
  with Loudnorm enabled (default on): the live loudnorm filter needs a moment
  to re-analyze a new position, and could briefly overcorrect before settling.
  The post-seek volume ramp now takes slightly longer, giving it a bit more
  room to stabilize before full volume returns.
- Fixed inconsistent loudness between stereo and multichannel sources by
  applying output-layout-aware normalization, downmix compensation, and
  controlled post-processing gain.
- **Embedded subtitles now load almost instantly**, even deep into large 4K
  files. Previously, subtitle extraction waited for the *entire rest of the
  file* to be processed before delivering any cues — on a multi-GB 4K file,
  this could mean 15-20+ seconds of delay depending on how far into the movie
  you activated the track. Cues are now delivered progressively as they're
  extracted.
- Fixed a rare subtitle text corruption that could occur at read-boundary
  edges when encoding detection (UTF-8 vs legacy Windows-1250) landed exactly
  on a split multi-byte character.
- **Fixed visible shimmer/tremor on edges**, most noticeable on lower-resolution
  sources (e.g. 720p): Detail Enhance had no noise gate and could react to
  compression noise frame-to-frame, especially at its wider (3-7px) sampling
  radius. RCAS sharpening's own noise gate was also strengthened for the same
  class of artifact.
- **Fixed leftover frame residue after scene cuts**: cut detection was based
  purely on luminance, so a cut between similarly-lit but differently-colored
  shots (e.g. warm → cool lighting) could slip through undetected, letting
  the temporal history briefly blend the old shot into the new one. Cut
  detection now also considers color (chrominance) change, not just
  brightness — cuts feel noticeably snappier as a result.
- Fixed high Stability values breaking the image into independently moving
  patches; extra history is now accepted only on static, color-consistent
  regions and is rejected around motion and scene changes.
- **Fixed the large black band that could appear at the bottom of the player**
  after loading several films with different resolutions in succession.
  Window/canvas dimensions and stale render targets are now refreshed for each
  source.

## [1.0.0] — Initial release

### Added
- GPU-accelerated real-time post-processing pipeline: RCAS / Adaptive
  sharpening, Deband, Detail Enhance, Film Grain, Deblock.
- Motion-Compensated TAA (temporal anti-aliasing) with optical-flow-driven
  history — stabilizes grain/noise without ghosting on movement.
- Pan Shutter and per-object motion blur for judder-free 24fps playback.
- Display Sync — refresh-rate-aware frame interpolation, with a ½ Rate option.
- HDR10 / HLG / Dolby Vision detection, tone mapping, and passthrough.
- GPU Upscale (Bicubic+) for lower-resolution sources on larger displays.
- Hardware decoding via WebCodecs (HEVC, AV1, VP9) with an FFmpeg fallback
  for legacy and incompatible formats.
- AstraGL Cast — LAN streaming to any device via VLC or any player that
  supports "Open Network Stream."
- Embedded & external subtitle support, loudness normalization, voice clarity
  boost, audio-track selection.
- License activation system — free trial (watermark + 15-minute sessions),
  unlocked permanently with a one-time purchase.
