# Changelog

All notable changes to AstraGL Player are documented here.

## [1.2.5] — 2026-09-24

### Motion
- **Interpolation has been massively improved.** Display Sync motion is smoother,
  cleaner and more stable across pans, action, animation and every kind of source.

- **Display Sync now combines two kinds of motion.** On camera pans and slow, steady
  movement it creates in-between frames for the smoothest motion. In busy, fast or
  chaotic action it keeps creating in-between frames, but moves them only part of
  the way, so the action still flows instead of stuttering while stray specks stay
  small. The switch between the two is gradual, and the player no longer flips back
  and forth between them several times a second in hectic scenes.

- **A sharper motion estimator for pans and steady movement.** While in-between frames
  are being created, motion is now traced with a patch-based estimator that follows
  plain surfaces, large lettering and walls far better, so pans no longer show doubled
  text or patches of the picture standing still.

- Objects moving in front of a still camera now move smoothly even on a plain
  background. Anything that moves too fast to follow while the camera is still or
  slow, such as a head popping in from the side or someone bursting through a
  door, is shown whole from the real frames instead of breaking up into blocks or
  smears.

- **Hand-drawn animation looks right.** Classic cartoons drawn "on twos" (each drawing
  held for two frames) are recognised automatically: slow movement now flows evenly
  from drawing to drawing instead of stopping and starting, and fast movement shows
  each drawing whole instead of smearing or doubling it. In animated films motion
  stays smooth across every scene, dissolves no longer make the picture shake, and
  Object Blur pauses so hand-drawn lines stay crisp (a note under its slider shows
  when).

- Films converted from 24 fps for television or video (25 or 29.97 fps, with a
  repeated frame every second or every fifth frame) are recognised automatically:
  the repeated frames are skipped, so pans no longer hitch at a regular beat.

- Videos with a variable frame rate, such as phone recordings, now move evenly:
  in-between frames follow the real timing of each frame instead of the nominal
  rate, so sections at 30 or 20 fps no longer stall and jump.

- Right after a scene cut, real frames are shown for up to one second while the
  motion of the new scene settles, so cuts no longer start with stray specks; when
  the new shot is clean, smooth motion returns sooner. Cuts are now detected
  reliably, while fast camera moves through colourful scenery are no longer
  mistaken for cuts. Flashes, explosions and laser blasts are still handled like
  cuts, so they never break up into fragments.

- End credits and titles over a nearly black picture are shown as clean real frames
  with an even hand-over, instead of being interpolated, so thin lettering never
  doubles or breaks.

- **No more dark patches at the edges of moving objects.** The film restoration
  filters no longer pull colour from the next frame onto the one being shown.

- **Much smoother Display Sync.** In-between frames now travel the full distance
  between the real frames, so motion flows evenly instead of sticking to each real
  frame and then jumping halfway through.

- **No more ghosting on fast motion.** Grey or coloured double images around fast
  objects are gone. Where motion is too fast to reconstruct, the affected area
  shows a whole real frame instead of a blend of two, so it never doubles.

- In chaotic action scenes, interpolation fades in gradually rather than cutting
  in abruptly, and keeps more of its smoothness than before.

- **Object Blur** has been rebuilt and is now on by default (0.50). It adds
  camera-style motion blur to objects moving on their own, so fine detail on fast
  action no longer shimmers, while slow motion and camera pans are left to stay
  sharp and to Pan Shutter. On shots where the camera circles a character or is
  handheld, it automatically blurs a little more to keep the background from
  shimmering.

- In chaotic action, areas that cannot be interpolated now ease from one real
  frame to the next over part of the interval, instead of jumping at once, so fast
  action judders much less.

- The motion safeguards never reduce interpolation below 20% of its range.

- Dark areas and black bars burned into the film no longer count as motion the
  player could not follow, so dark scenes are no longer softened by extra Object
  Blur, and the `C` overlay reports interpolation more accurately.

- **Pan Shutter** has been redesigned. It now stretches a single real moment along
  the camera movement instead of blending two frames, which removes vibrating
  edges and double images on pans. The default is raised to 1.50.

- Burned-in subtitles and on-screen logos stay sharp during fast pans instead of
  smearing or doubling.

- **Muzzle flashes, lightning and explosions** no longer break up into a mosaic
  with patches of the background showing through. Sudden bursts of light are
  shown as real frames instead of being interpolated.

- Thin, bright details such as neon signs stay clean during camera pans, without
  pixel doubles next to them.

- Rare small specks during interpolation are much less frequent: small patches of
  motion that disagree with everything around them are now replaced by the
  surrounding motion.

- No more stray specks on handheld camera moves, such as a camera circling around a
  character. The detail filters now ease off on moving areas, where the film is
  already motion-blurred.

- Characters followed by the camera keep clean outlines against detailed
  backgrounds, without jagged edges, double images or outline halos from Pan
  Shutter. On very fast pans, interpolation eases off gradually so that foliage
  and other fine backgrounds do not tear.

- No more speckles at scene cuts, and pausing always shows the real frame.

- **Display Sync** picks its own cadence for your monitor. The separate ½ Rate
  button is gone; Display Sync handles it automatically.

- A frame-budget watchdog keeps playback smooth. With Super Upscale 8K it halves
  the frame rate first and only gives up upscaling if that is still not enough; on
  systems that still cannot keep up, interpolation switches off with a notice
  rather than dropping frames.

- The analysis of each new film frame is now spread over two refreshes instead of
  one, so 4K films with Super Upscale 8K on high-refresh monitors no longer drop
  frames or fall back to half rate, and missed refreshes are much rarer. On WebCodecs SDR films the motion analysis for the
  next frame is also prepared in advance, from the frame already decoded, so the
  refresh where a new frame appears costs about as much as any other.

- **The picture no longer freezes while the sound carries on** on systems whose
  video decoder cannot keep up with a demanding film. The player now frees the
  graphics card for decoding — first lowering the Display Sync cadence, then
  pausing Super Upscale 8K, then Display Sync, then
  switching 4K films to 4K GPU Downscale — and resynchronises the picture with the
  sound. These changes apply to the current film only; your settings are kept.

### Picture
- **Detail controls** — **Fine Detail** (the finest, one-pixel detail), **Medium
  Details** (4 pixel shapes) and **Large Details** (8 pixel shapes) restore shape
  and texture rather than only sharpening edges, with halo control. Fine Detail
  tells real structure from grain, so grain is left alone. Negative values soften
  a scale instead of enhancing it.

- **Details Auto** — sets all three detail controls by itself, measuring how soft
  the source is and adapting slowly over the film.

- **Color Stability** — steadies the slow colour drift typical of old TV masters.

- **White Balance** — removes a colour cast measured across the whole film. Off by
  default and intended for old or faded films.

- Sharpening, Detail Enhance and Micro Contrast now have a clearly visible effect.

- New defaults: Sharpen in Thin Edges mode at 1.50, Detail Enhance 0.30, Spatial Smooth off, and Film
  Grain 0.50 below 1080p.

- **Temporal Detail** now also works on 1080p films (previously up to 720p and
  1080i), with a gentler setting suited to already sharp sources. It works
  together with Super Upscale 8K.

- **Ambient Fill** — fills the black bars around the film with a soft, dimmed glow
  that continues the edges of the picture, like a light behind the screen. It also
  detects black bars burned into the file itself. Off by default; the film itself
  is not changed.

- **Edge AA** now uses FXAA-style edge reconstruction: it follows each stair-step along an
  edge at any angle, so jagged nearly flat lines and curves below 1080p come out smooth
  instead of only being softened. The slider now scales the effect linearly up to 1.00,
  and the default follows the source: 0.80 up to 720p, 0.50 between 720p and 1080p.
- **Chroma Fix** has been removed.

### Performance
- With Super Upscale 8K, missed frames are now rare: video decoding is scheduled
  into the gap between frames instead of landing right when a frame is due.

- No more brief stutter a few seconds after a film starts. The optimised picture
  processing is now prepared while the player opens and switched in at a scene cut,
  a pause or a seek, where the short pause it needs cannot be seen.

- While the player opens and prepares its picture processing, a green
  "Loading shaders, please stand by" message is shown, so the short wait no longer
  looks like a freeze.

- Picture processing during interpolation needs noticeably less graphics time,
  and filters that are switched off no longer cost any frame time. Both leave
  more headroom on modest graphics cards.

### Diagnostics
- A frame-cost bar in the `C` overlay, from green through orange to red, shows how
  much of the frame budget each frame uses.

- A **Display Sync** section in the `C` overlay shows how much interpolation is
  actually applied, what is reducing it, and the current Object Blur strength.

- The `C` overlay shows how long the last seek took and whether any seek failed.

### Fixed
- HDR Passthrough made the picture look washed out and grey, because the current
  graphics engine cannot output HDR. It is now unavailable until the engine
  supports HDR, and HDR films use tone mapping instead. SDR films also kept
  Brightness and Contrast locked while it was on.

- Object Blur showed 1.00 in Settings while its real value was 0.

- Object Blur no longer leaves dark specks inside bright muzzle flashes.

- After a seek, the picture could stay frozen and the Play button stop responding
  if the video stream failed to restart. The player now retries by itself, and
  Play restarts the stream.

- After a long pause, the picture could freeze a few seconds into playback until
  you skipped back. If the video stream stops arriving during playback, the player
  now restarts it from the current position by itself. Reaching the end of a film is
  no longer mistaken for a stalled stream.

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