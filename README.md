<div align="center">

# 🎬 AstraGL Player

**A GPU-accelerated desktop video player with real-time WebGL post-processing.**

Clean, sharp, smooth playback — every post-processing filter runs live on the GPU.

[⬇️ Download latest release](../../releases/latest)

</div>

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

### Color & Tone
- **Contrast**, **Gamma**, **Saturation**, **Shadow Lift** — global grading.
- **Highlight Boost** — SDR→HDR highlight lift for HDR monitors viewing SDR
  content.

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
- **De-telecine (IVTC)** — removes 3:2 pulldown from NTSC DVD content.

### Audio
- **Voice Clarity** — dynamic compressor that lifts dialogue over music/effects.
- **Loudnorm (EBU R128)** — normalizes loudness to −19 LUFS, evening out quiet
  scenes vs. loud action.
- **Audio-track selection** and a **default audio-language** preference
  (auto-picks your language on open, falls back to English, then the first track).
- Volume boost beyond 100% via the Web Audio API.

### Subtitles
- Embedded and external subtitle files.
- Adjustable size and **drag-to-reposition** (Ctrl + click/drag).

### Extras
- **AstraGL Cast** — stream the current file to a TV/device on your LAN.
- Frameless custom title bar, fullscreen, and a polished floating control panel.

---

## ⌨️ Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `Space` | Play / Pause |
| `←` / `→` | Seek −10s / +10s |
| `Ctrl + ←` / `Ctrl + →` | Step one frame back / forward |
| `M` | Mute |
| `Numpad 4 / 5 / 6` | Aspect ratio narrower / reset / wider |
| Double-click | Toggle fullscreen |

---

## 🚀 Download & Run

1. Download `AstraGLPlayer.exe` from the [Releases](../../releases) page.
2. Run it — no installation needed (portable).
3. On first launch, the app will offer a one-click **"Download FFmpeg"** button.
   FFmpeg is required for legacy and incompatible formats (AVI, DivX, MPEG-2,
   AC3/DTS audio, etc.) — used for demuxing, transcoding, and re-encoding on the
   fly so the player can handle files that WebCodecs cannot decode natively.
   Fetched directly from [gyan.dev](https://www.gyan.dev/ffmpeg/builds/) (~190 MB,
   one-time download, stored locally).

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
- ☕ Support: [ko-fi.com/sonicvibes](https://ko-fi.com/sonicvibes)
