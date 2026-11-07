# Skill 001 — Realistic Hair Motion & Clean-Plate Workflow

## Purpose

Create an editable KeyFlow project in which selected hair regions move while the face, eyes, skin, accessories not assigned to the hair, clothing, hands, body, and background remain stationary.

The workflow has two independent requirements:

1. **Clean extraction:** isolate the hair with an anti-aliased alpha mask and remove the original hair from the base image.
2. **Controlled motion:** animate only the extracted hair layers, with pivots near the roots and delayed movement toward the tips.

A GIF or flattened video is not a substitute for the editable `.kf` project.

## Required inputs

- Original, highest-resolution illustration.
- A clear definition of which regions count as hair.
- KeyFlow project template or a verified `.kf` sample when the project format is not documented.

## Workflow

### 1. Inspect the source

- Record the image dimensions and aspect ratio.
- Map hair silhouettes, hairline boundaries, overlaps with faces, and strands crossing other objects.
- Decide whether ornaments move with the hair or remain fixed.
- Never assume a color threshold alone is a complete hair mask.

### 2. Segment the hair

- Start with hand-traced silhouette polygons.
- Use color segmentation and GrabCut as supporting signals, not as unquestioned truth.
- Exclude eyes, skin, facial features, hands, and clothing explicitly.
- Expand the mask slightly to capture dark ink outlines and anti-aliased edge pixels.
- Inspect the mask over the original at 100% zoom. Fix missed wisps and accidental face pixels.

### 3. Build a clean plate

- Remove the original hair from a copy of the source image using inpainting or manual reconstruction.
- Preserve all pixels outside the removal mask.
- Hidden content under hair cannot be recovered exactly from a single image. Keep movement conservative where the clean plate is uncertain, or manually reconstruct the exposed region.
- Do not claim the clean plate is perfect merely because the static composite looks correct.

### 4. Extract transparent hair layers

- Copy source pixels through the refined mask into an RGBA PNG.
- Use a small, controlled edge feather; excessive feathering creates halos.
- Separate independently moving groups when possible: root, mid-length, and tips.
- Keep overlapping groups aligned to avoid seams.

### 5. Animate in KeyFlow

- Place the clean plate at the bottom of the layer stack.
- Place extracted hair layers above it.
- Keep the pivot/anchor near the hair root.
- Use smaller displacement at the root and larger displacement toward the tips.
- Use staggered timing for secondary motion; tips should lag behind the root.
- Prefer Bezier easing for soft acceleration and deceleration.
- Make the first and last poses match when a seamless loop is required.
- Use motion blur sparingly; it must not hide bad masks or unwanted movement.

### 6. Validate

- Compare the static layered composite with the original image.
- Inspect the maximum positive and negative motion poses.
- Check for old-hair ghosts, holes, halos, seams, exposed inpaint artifacts, and accidental movement of non-hair pixels.
- Verify the project opens in KeyFlow and remains editable.
- If the app cannot be run in the current environment, state that limitation and ask for an in-app validation screenshot.

## First challenge implementation — 2026-11-07

The first test uses a chibi illustration with red short hair and long blue hair.

- Source artwork: 1536 × 1152, 4:3.
- Project target: KeyFlow 1.5.6, 30 FPS, 4:3, 1080p.
- Layers: clean plate, red hair cutout, blue hair cutout.
- Animation: transform-based rotation and position keyframes with Bezier interpolation.
- Red hair: approximately ±1.35° around a crown anchor.
- Blue hair: approximately ±3° around a scalp anchor.
- Duration: 5 seconds.

### Known limitation

This first implementation uses layer transforms, not true strand-by-strand physics or a verified Puppet Pin mesh. It is a functional baseline for testing clean extraction, clean-plate coverage, layer order, and controlled sway. The next iteration should split hair into root/mid/tip groups or use a validated Puppet Pin mesh to create non-rigid deformation.

## Acceptance criteria

A build is accepted only when:

- The `.kf` project opens in KeyFlow.
- The original hair is fully covered by the extracted layers at rest.
- Only intended hair layers move.
- No visible duplicate hair remains at the original position during motion.
- No face, eye, skin, accessory, body, clothing, hand, or background movement is introduced unless explicitly assigned to the hair.
- The user has inspected the rendered result in KeyFlow.
