# .pz Extractor — ClipMaker Project Inspector

A single-file, client-side tool for opening **ClipMaker (`CM`) `.pz` project files**, inspecting their contents, and previewing the animated 3D text object in-browser.

Nothing is uploaded anywhere — the file is decompressed, parsed, and rendered entirely in your browser.

---

## What it does

- **Unpacks `.pz` files.** A `.pz` is a gzip-compressed tar archive. This tool gunzips it (via [pako](https://github.com/nodeca/pako)) and parses the tar entries manually (no server round-trip).
- **Decodes every entry** (`pz/basics`, `pz/object0`, `pz/object0_appearance`, `pz/camsequence`, `pz/audio`, `pz/scenes`, `pz/fx`, `pz/objects`, `pz/id`, `pz/pz`) as JSON where possible, and shows raw text otherwise.
- **Shows project stats at a glance**: tool/version, resolution, framerate, duration, object count, compressed vs. uncompressed size.
- **Font mapping.** ClipMaker's bundled font names (Absender, Bebas, VT323, Playfair Display, etc.) are mapped to the closest available web font — exact Google Fonts matches are flagged, proprietary/bundled fonts get a "mapped →" lookalike.
- **Live 3D preview** (three.js): the actual text, color, emissive glow, and material properties from the project are rendered as a real extruded 3D object — built from stacked alpha-masked planes so the extrusion follows the glyph shapes, not a bounding box.
- **Frame-accurate playback**: position/rotation/scale/color are sampled directly from the project's own keyframe tracks (with looping interpolation), and the camera follows the project's own camera keyframes plus its procedural shake settings. Includes play/pause, a frame scrubber, and a free-orbit toggle.
- **Keyframe views**: a piano-roll (colored rows + note ticks along the timeline) and a spreadsheet-style table (one row per keyframe, one column per property) for exact values.

## Usage

1. Open `pz_extractor.html` in any modern browser (Chrome, Firefox, Edge, Safari).
2. Drag a `.pz` file onto the drop zone, or click it to browse.
3. Explore:
   - **Archive Contents** — click any file to see its decoded detail.
   - **3D Preview** — play/pause, scrub frames, toggle camera shake / keyframe animation / grid / orbit mode.
   - **Keyframe Spreadsheet & Piano Roll** — shown under `object0`, `object0_appearance`, and `camsequence`.

No installation, build step, or server required — it's one HTML file.

## Tech

| Piece | Library |
|---|---|
| Gzip decompression | [pako](https://cdnjs.cloudflare.com/ajax/libs/pako) |
| 3D rendering | [three.js](https://threejs.org/) r128 (+ OrbitControls) |
| Fonts | Google Fonts (loaded on demand, mapped from ClipMaker font names) |
| Tar parsing | hand-rolled (ustar format) |

## Known limitations

- Only reads `.pz` (or raw `.tar`/`.gz`) files in the ClipMaker format described above; other Panzoid/ClipMaker export types aren't handled.
- Fonts not on the mapping list fall back to a generic sans-serif.
- Audio and video source references are shown as metadata only — no embedded media is extracted or played, since ClipMaker projects don't bundle the actual media file inside the `.pz`.
- The 3D preview approximates ClipMaker's renderer; it is not a pixel-perfect reproduction of the original export.
