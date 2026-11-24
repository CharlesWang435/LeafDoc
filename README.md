<div align="center">

# LeafDoc

**A $50 smartphone instrument for backlit field leaf imaging and AI plant-health diagnosis.**

<img src="media/leafdoc-device.webp" width="420" alt="The LeafDoc handheld enclosure with a phone mounted, showing a gallery of diagnosed corn leaf sessions"/>

*Purdue ABE · 3D-printed hardware + Android app · deployed in a Purdue–DARPA field study (6,500+ captures) · next-generation LeafStudio lightbox in build*

</div>

> **Project showcase.** This repository documents what LeafDoc does and how it was designed and built. The app source code and the hardware CAD are private. For collaboration or a demo, [get in touch](https://charleswang435.github.io).

---

## Why build it

Field photos of leaves are hard to score consistently. Lighting changes with the sun and clouds, the camera angle changes with every shot, and the symptoms that matter most, such as interveinal chlorosis, water-soaked lesions and mesophyll damage, are often ambiguous on the leaf surface.

LeafDoc fixes the geometry and the light. A handheld enclosure clamps over the leaf and backlights it, so the phone captures it in **transmittance**: light passing *through* the blade. Internal damage that is hard to see in a reflected-light photo becomes clearly visible, and every image in a trial is taken at the same distance, angle and illumination. The app then tags, stitches and diagnoses the images, so what leaves the phone is a usable scientific record rather than a snapshot.

<div align="center">
<img src="media/leafdoc-stitched.webp" width="100%" alt="A corn leaf reconstructed from six backlit frames into one continuous strip"/>
<br/><sub>One corn leaf, six backlit frames stitched into a continuous strip</sub>
</div>

---

## Build log

### 1. A fixed, backlit imaging geometry
A spring-loaded clamp holds the leaf flat against a backlit window, and the phone mounts on top so its camera looks straight down onto the lit blade. A leaf longer than the window is drawn through the slot section by section, and the app captures each section as a frame.

<div align="center">
<img src="media/leafdoc-cad.webp" width="640" alt="CAD render of the enclosure: a leaf passing through the imaging slot beneath a clamped phone mount"/>
</div>

### 2. Iterating the enclosure
The enclosure went through several print revisions before settling on the clamp-and-handle design used in the field.

<div align="center">
<img src="media/leafdoc-revisions.webp" width="100%" alt="Successive 3D-printed enclosure revisions arranged left to right, ending in the current handheld clamp design"/>
</div>

### 3. An app built for data, not photos
The Android app (Kotlin, Jetpack Compose, CameraX + Camera2) turns the phone into a manual instrument:

- **Manual camera control:** ISO, shutter, exposure compensation, white balance, zoom-based lens selection (0.6× to telephoto), five focus modes with AF lock, live histogram and grids, and full-sensor maximum-resolution capture where the phone allows it.
- **Lossless formats:** maximum-quality JPEG, PNG, TIFF from a hand-written encoder with no third-party dependency, and RAW/DNG from the sensor. Android can't decode TIFF or DNG, so the app previews a JPEG proxy and exports the original byte-for-byte.
- **Structured metadata:** farmer, field, treatment and leaf number on every capture, reusable pick lists, and automatic GPS.
- **Two field workflows:** *Simple* mode for one image per leaf with an always-visible metadata bar, and *Detailed* mode for multi-frame specimens kept un-stitched so the original frames stay the record.
- **Stitching:** horizontal panoramas with gradient blending and optional midrib alignment, which finds the leaf vein and corrects vertical drift between frames.

<div align="center">
<table><tr>
<td align="center"><img src="media/leafdoc-app-capture.webp" width="200" alt="Backlit leaf capture screen with a live histogram"/><br/><sub>Backlit capture with live histogram</sub></td>
<td align="center"><img src="media/leafdoc-app-frames.webp" width="200" alt="Stitched leaf result above its source frames"/><br/><sub>Stitched result and source frames</sub></td>
<td align="center"><img src="media/app/simple-mode.webp" width="200" alt="Start New Session dialog with Simple and Detailed session types"/><br/><sub>Simple or detailed sessions</sub></td>
</tr><tr>
<td align="center"><img src="media/app/dashboard.webp" width="200" alt="Overview dashboard with session, frame and diagnosis counts"/><br/><sub>Overview dashboard</sub></td>
<td align="center"><img src="media/app/formats.webp" width="200" alt="Settings screen with JPEG, PNG and TIFF capture formats"/><br/><sub>Capture formats</sub></td>
<td align="center"><img src="media/leafdoc-app-ai.webp" width="200" alt="AI diagnosis screen with health score and disease probabilities"/><br/><sub>AI diagnosis</sub></td>
</tr></table>
</div>

### 4. AI diagnosis that compares across models
Diagnosis runs through a provider-agnostic layer, so the vision model (Google Gemini, Anthropic Claude or OpenAI) is a setting rather than a rebuild. Four prompt templates, from a quick check to a research-grade report, are written specifically for transmittance imagery and share one JSON output contract. Every result has the same shape (health score, primary diagnosis with confidence, per-disease probabilities, treatment notes), so results can be compared across models and templates.

### 5. In the field
LeafDoc was deployed in a Purdue–DARPA study, collecting more than 6,500 captures.

---

## Next: the LeafStudio lightbox

**LeafStudio** (formerly LeafSpec RGB) is the second-generation enclosure, in build as Proto 1 (November 2026). Instead of a slot the leaf is drawn through, it is a light-tight box that gives any smartphone a controlled "studio" for a whole leaf section.

<div align="center">
<img src="media/hardware/assembly-closed.webp" width="640" alt="CAD view of the LeafStudio lightbox, closed, with a phone clamped on the lid and the pull handle on the right"/>
</div>

- **Light-tight box** with a backlit, diffused 15 × 15 cm window; the phone lies flat on the lid and looks straight down through it.
- **High-CRI lighting:** eleven rows of 4000 K, CRI 90 COB LED strip under an opal diffuser, powered from a battery in the handle.
- **Lights only when it matters:** the LEDs switch on only when the power is on *and* the box is closed, which saves battery and keeps ambient light out of every capture.
- **One-handed operation:** pulling the spring-loaded handle lifts the lid straight up on a parallelogram linkage, and a push-push latch holds it open for loading.
- **Adjustable phone mount:** a spring clamp on a sliding shuttle lines up any phone's camera with the window.
- Printed in carbon-fibre PLA, with a felt-and-magnet rim seal to block stray light.

<div align="center">
<table><tr>
<td align="center"><img src="media/hardware/light-box-led-plate.webp" width="260" alt="Top view of the LED plate with 11 LED strips"/><br/><sub>LED plate</sub></td>
<td align="center"><img src="media/hardware/clamp-lugs-shuttle.webp" width="260" alt="Phone clamp on its sliding shuttle"/><br/><sub>Phone clamp shuttle</sub></td>
<td align="center"><img src="media/hardware/lid-wordmark-leafstudio.webp" width="260" alt="LEAFSTUDIO wordmark with the three-leaf mark, engraved on the lid top"/><br/><sub>Lid wordmark</sub></td>
</tr></table>
</div>

---

## Tools and stack

| Area | Stack |
| --- | --- |
| Hardware | Autodesk Fusion CAD, FDM printing (PLA-CF), COB LED lighting, 9 V battery with boost converter |
| App | Kotlin, Jetpack Compose (Material 3), MVVM, Hilt, Room, DataStore, Coroutines and Flow |
| Camera | CameraX with Camera2 interop, max-resolution and RAW/DNG capture |
| AI | Provider-agnostic interface over Gemini, Claude and OpenAI vision APIs |

---

<div align="center">
<a href="https://charleswang435.github.io">Portfolio</a> · <a href="https://github.com/CharlesWang435">GitHub</a>
</div>
