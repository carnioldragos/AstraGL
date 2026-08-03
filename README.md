<div align="center">

# 🎬 AstraGL Player

**A GPU-accelerated desktop video player with real-time WebGL post-processing.**

Clean, sharp, smooth playback — every post-processing filter runs live on the GPU.

[⬇️ Download latest release](../../releases/latest) · [📋 What's new in v1.1.0](CHANGELOG.md)

</div>

---

> 💡 **14-day full trial** — the complete player, with every enhancement enabled,
> no watermark, and no playback/session limit. After the 14 calendar days it
> keeps working as a demo (5 minutes of playback per launch, with a watermark)
> until activated. Internet is required once to register the trial; afterward it
> can be used offline.
> [Get the full license here](https://carnio0.gumroad.com/l/mtmhki).

---

## Overview

AstraGL Player is an Electron + WebGL2 video player built around a real-time
shader pipeline. Instead of baking effects into a re-encoded file, every frame
is decoded, uploaded to the GPU, and processed live through a chain of
post-processing shaders — temporal anti-aliasing, sharpening, debanding, color
grading, HDR tone mapping and more — all adjustable on the fly while the movie
plays.

It combines hardware decoding (WebCodecs for modern codecs, FFmpeg for legacy
and exotic formats) with a custom GPU pipeline, giving you a single player that
handles everything from old AVI/DivX rips to 4K HDR HEVC.

---

## ✨ Features

### Image (spatial) filters
- **Spatial Smooth** — edge-preserving bilateral filter; cleans film noise while
  keeping fine detail.
- **RCAS Sharpen** — AMD FSR1-style Robust Contrast Adaptive Sharpening:
  per-channel weighting, Laplacian noise gate, and 3×3 anti-ringing clamp (zero
  halos).
- **Deband** — removes banding on gradients (skies, fades) with adaptive dither,
  protected on textured areas.
- **Detail Enhance** — multi-radius local-contrast ("clarity") for depth and pop.
- **Deblock** — conservative 8×8 block-artifact smoothing for low-bitrate sources.
- **Film Grain** — subtle synthetic grain to mask banding and add texture.

### Temporal filters (motion-compensated)
- **MC-TAA (Stability)** — Temporal Anti-Aliasing driven by Lucas–Kanade optical
  flow. Reduces flicker/shimmer and stabilizes the image without ghosting,
  using motion-compensated history (the *same surface* across frames, not the
  same screen pixel).
- **Pan Shutter** — synthetic camera shutter along the global pan vector;
  removes 24fps pan judder, edge-aware so flat areas stay sharp.
- **Object Motion Blur** — per-pixel directional blur on moving objects (using
  the optical-flow field), independent of camera motion.

### Depth & presentation
- **Perceptual Depth** — a sense of depth on ordinary 2D video produced purely
  through light and colour: **no AI, no depth map, no optical flow and no
  geometric warp**. It combines adaptive lens separation, surface-volume
  modelling, edge-safe micro depth-of-field, atmospheric bloom, and a
  luminance-preserving variant of Chromadepth — nearer planes pushed slightly
  warmer, distant planes slightly cooler, exploiting the eye's own chromatic
  aberration. Since no pixel is ever displaced, it cannot produce the tearing,
  doubling or smearing that displacement-based 2.5D effects suffer from, and it
  looks identical whether the film is playing or paused.

  Internally it layers several stages, all luminance-preserving: *surface-volume
  modelling* derives shading from smooth depth gradients so curved objects gain
  perceived volume (restricted to stable surface interiors, so silhouettes never
  turn into emboss or halos), while *atmospheric separation* keeps nearer planes
  slightly warmer and more saturated and distant planes cooler and softer. One
  toggle — no sliders to tune.

### Color & Tone
- **Contrast**, **Gamma**, **Saturation**, **Shadow Lift** — global grading.
- **Highlight Boost** — SDR→HDR highlight lift for HDR monitors viewing SDR
  content.
- **Micro Contrast** — restrained local tonal separation for legacy SD sources,
  without crushing blacks or clipping highlights.

### HDR
- Automatic detection of **HDR10 / PQ / HLG** and **Dolby Vision**.
- **HDR → SDR tone mapping** with multiple operators, plus contrast and
  red/yellow recovery controls.
- **HDR Passthrough** for native output on HDR displays.

### Motion smoothness
- **Display Sync** — continuous, refresh-rate-aware frame interpolation that
  eliminates judder when your refresh rate isn't an integer multiple of the
  video's fps (e.g. 24fps on a 144Hz screen).
- **½ Rate** — halves the number of interpolated frames to dial back the
  "soap-opera" look while keeping cadence smooth.

### Decoding & formats
- **WebCodecs hardware decode** for HEVC / H.265, AV1, VP9.
- **FFmpeg** transcode/stream path for legacy and incompatible codecs (AC3/DTS
  audio, MPEG-2, DivX/XviD, VC-1, etc.).
- **Direct playback** for browser-native formats.
- Containers: **MKV, MP4, AVI, MOV, WMV, TS/M2TS, WebM, MPG/MPEG, VOB, FLV** and
  more.
- **GPU Upscale (Bicubic+)** — Mitchell–Netravali bicubic upscaling for SD/720p
  on 4K displays.
- **Super Upscale 8K** — experimental final reconstruction pass at up to
  7680×4320, preserving the source aspect ratio. Decode and the main filter
  chain stay at source resolution, so the cost is far lower than running the
  whole pipeline at 8K. Driver texture/renderbuffer limits are checked
  automatically and the option disables itself on unsupported hardware.
- **4K Conversion Downscale** — optional hardware path that converts 4K SDR/HDR
  to 1440p for more headroom on lighter GPUs.
- **De-telecine (IVTC)** — removes 3:2 pulldown from NTSC DVD content.

### Audio
- **Voice Clarity** — dynamic compressor that lifts dialogue over music/effects.
- **Loudnorm (EBU R128)** — normalizes loudness to −19 LUFS, evening out quiet
  scenes vs. loud action.
- **Audio Output** — Auto / Stereo / Surround. Auto keeps multichannel output
  when the device supports it, Stereo performs a dialogue-safe downmix, and
  Surround preserves 5.1/7.1 (converting browser-incompatible formats such as
  DTS to multichannel AAC when needed).
- **3D Enhance** — Mid-Side stereo widening with automatic center-channel
  compensation, so dialogue stays forward as the soundstage widens.
- **Bass Boost** — low-frequency shelf, 0–8 dB.
- **Audio-track selection** and a **default audio-language** preference
  (auto-picks your language on open, falls back to English, then the first track).
- Volume boost beyond 100% via the Web Audio API.

### Subtitles
- Embedded and external subtitle files.
- Adjustable size and **drag-to-reposition** (Ctrl + click/drag).

### Extras
- **AstraGL Cast** — stream the current file to any device on your LAN (Android,
  smart TV, another PC) via VLC or any player that supports "Open Network Stream."
- **Instant A/B comparison** (`A`) — cycles between the untouched original, a
  vertical split-screen, and normal playback. Both sides share identical framing
  and filters, so the visible difference is only what you're evaluating.
- **Diagnostics overlay** (`C`) — source/decoded/render resolution and active
  render path, color pipeline and bit depth, frame pacing against the display's
  VSync budget, audio route and buffer health. A second press reveals the
  internal view (decoder queues, per-stage GPU timings, audio event counters).
- Frameless custom title bar, fullscreen, and a polished floating control panel.

---

## ⌨️ Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `Space` | Play / Pause |
| `←` / `→` | Seek −10s / +10s |
| `Ctrl + ←` / `Ctrl + →` | Step one frame back / forward |
| `M` | Mute |
| `A` | A/B comparison — original, split-screen, normal |
| `C` | Diagnostics overlay — hidden, essential, full |
| `G` | GPU debug views — per-filter masks (smoothing, TAA, sharpen, deband…) |
| `Numpad 4 / 5 / 6` | Aspect ratio narrower / reset / wider |
| Double-click | Toggle fullscreen |

---

## 💻 Requirements

- Windows 10 / 11 (64-bit)
- GPU: NVIDIA RTX 20-series / AMD RX 5000-series or newer
- The app itself is ~250 MB unpacked; FFmpeg (optional, see below) is a separate ~160 MB
  download that unpacks to ~400+ MB on disk

---

## 🚀 Download & Run

1. Download **`AstraGL Player v1.1.0`** from the [Releases](../../releases)
   page — a ready-to-play kit, no installation needed (portable).
2. Unzip it and run `AstraGL Player.exe`.
3. On first launch, the app will offer a one-click **"Download FFmpeg"** button.
   FFmpeg is required for legacy and incompatible formats (AVI, DivX, MPEG-2,
   AC3/DTS audio, etc.) — used for demuxing, transcoding, and re-encoding on the
   fly so the player can handle files that WebCodecs cannot decode natively.
   Fetched directly from [gyan.dev](https://www.gyan.dev/ffmpeg/builds/) (~160 MB
   archive, ~400+ MB once unpacked, stored locally).

---

## 🔓 Full License

The 14-day trial is fully functional, without a watermark or session limits.
Trial registration sends only a pseudonymous SHA-256 identifier derived locally
from the Windows installation ID, plus the app version. The original Windows
identifier, name, email, and media information are never sent. Internet is
required only once to obtain the signed trial token.
After the 14 days the player continues as a demo — 5 minutes of playback per
launch, with a watermark — so your files never become unplayable. Remove every
limit permanently with a one-time license:
**[carnio0.gumroad.com/l/mtmhki](https://carnio0.gumroad.com/l/mtmhki)**

---

## 🧱 Tech Stack

- **Electron** (Chromium runtime + Node)
- **WebGL2** — multi-pass shader pipeline (TAA, optical flow, CAS, color, HDR)
- **WebCodecs API** — hardware video decode
- **FFmpeg / FFprobe** — demux, transcode and probe for non-native formats
- **mp4box.js** — fragmented-MP4 parsing for the WebCodecs path

---

## 📜 License & Credits

Original application code © **Carniol Dragos** ("Sonic Vibes").
Third-party components and their licenses are listed in
[`THIRD_PARTY_LICENSES.txt`](THIRD_PARTY_LICENSES.txt) (Electron, FFmpeg,
MP4Box.js, 7-Zip, etc.). No GPL-licensed binaries are bundled with the app.

- **Developed by** — Sonic Vibes *aka* Carniol Dragos · carnioldragos@gmail.com
- **Beta testing** — Rizea Valentin
- ☕ Support us: [ko-fi.com/sonicvibes](https://ko-fi.com/sonicvibes)
