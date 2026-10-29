# Vinyl Coaster Maker

🇹🇷 [Türkçe sürüm](README.tr.md)

To use without downloading:
https://goblinhanyikan.github.io/3dNfcVinylMaker/

A browser-based tool that turns any image into a set of multicolor STL files for a 3D-printed coaster. The coaster is Ø100 mm × 3 mm with a black rim, your image on both faces, and a sealed internal cavity where you can embed an NFC tag by pausing the print.

Everything runs locally in your browser. Your image is never uploaded.

## How it works

The color pipeline follows the method of [goblinhanyikan/PNGtoSVG-Multicolor-Printing](https://github.com/goblinhanyikan/PNGtoSVG-Multicolor-Printing) (MIT license). It is a re-implementation of that method, not a copy of its code.

1. **Crop and flatten.** The image is cropped to a square (zoom and shift sliders), scaled to the chosen resolution, and flattened onto black. Transparent areas therefore print black.
2. **Quantize.** The colors are reduced to a small palette (2–8) with k-means++ clustering.
3. **Mask.** Every pixel is assigned to its nearest palette color, producing one binary mask per color. A light majority filter removes isolated specks.
4. **Extrude.** Each mask is merged into rectangles and extruded into boxes, then written as its own STL.
5. **Assemble.** All parts share one coordinate system, so they line up when imported together into a slicer.

### Coaster structure (bottom to top)

| Z range (mm) | Content |
|---|---|
| 0 – 0.6 | Image layer (colors) inside Ø80, black rim from Ø80 to Ø100 |
| 0.6 – 1.0 | Solid black |
| 1.0 – 2.0 | Sealed NFC cavity (Ø25, 1 mm tall by default) inside a black wall |
| 2.0 – 2.4 | Solid black ceiling |
| 2.4 – 3.0 | Image layer (colors) inside Ø80, black rim |

The underside image is mirrored so it reads correctly when the coaster is flipped like a page.

## How to use

1. Open the app. Use the **TR / EN** button to switch language.
2. Upload an image. A sample image is loaded by default.
3. Adjust zoom and shift so the important part of the image sits inside the circle.
4. Choose the number of colors. Fewer colors print more reliably, and flat artwork and logos work best.
5. Optional: tick **Add text around the rim**, then pick text, font (or upload one), color, size, layout and which faces get text.
6. Click **Download STL set (.zip)** and unzip it.

### Settings

| Setting | Default | Meaning |
|---|---|---|
| Colors | 4 | Palette size (2–8) |
| Resolution | 400 px | Pixels across the image. At Ø80 mm, 400 px is 0.2 mm per pixel |
| Speckle cleanup | 1 | Number of majority-filter passes |
| Image diameter | 80 mm | Diameter of the image area. The rest is black rim |
| NFC cavity diameter | 25 mm | Diameter of the sealed pocket |
| Cavity height | 1 mm | Height of the pocket, centered in the thickness |
| Image layer thickness | 0.6 mm | Thickness of the colored layer on each face |

### Text on the rim

- Text is inlaid in the rim layers, so the coaster stays 3 mm thick.
- Layouts: **centered at top**, **spread around the full ring**, or **repeated around the ring**.
- The text sits in a band 0.8 mm inside both edges of the rim. If the rim is narrower than 4 mm, text is disabled.
- It exports as `text_<color>.stl`, and the black rim gets matching gaps.

## Printing

1. Import every STL into the slicer **together**, and do not move or rescale them.
2. Assign one filament per file: `base_black` gets black, each `color*` file gets its color, and `text_*` gets the text color.
3. **Insert the NFC tag.** Add a pause (or filament change) at the Z height shown in the app, which is 2.0 mm by default. This is the top of the cavity, just before the ceiling prints. Place the tag flat in the pocket, then resume.
4. The ceiling bridges a 25 mm span. This normally prints without supports, but run a test first.

Tips: leave 0.2 mm of clearance around the tag if it is a snug fit, and check that the tag is thin enough for the cavity height.

## The color system

- **Palette:** k-means++ clustering in RGB space. The initial centers are chosen by k-means++, followed by 10 refinement iterations on up to about 15,000 sampled pixels. Every pixel then takes the nearest center by Euclidean distance. This is the same approach the reference repo uses for its "full color" mode.
- **One STL per color:** the same idea as the reference repo's "3D print friendly" mode. Each color is a complete layer in its own file, so the files reassemble into the original picture without gaps.
- **Black:** the rim and body are structural black. If the image itself contains black, that becomes one of its palette colors, so you can give it the same filament.
- **App theme:** purple accent (`#6d3fc4` light, `#8b5cf6` dark) on a softly purple-tinted neutral background. It follows the system light or dark setting.

## Limitations

- Output is pixel-stepped (rectilinear), like the reference tool's default mode. Photos come out posterized.
- Text edges are pixel-stepped at 0.1 mm, and thin fonts may lose fine strokes.
- The tool has not been validated on every slicer. Check the layer preview before a long print.

## Credits and license

Color method based on [PNGtoSVG-Multicolor-Printing](https://github.com/goblinhanyikan/PNGtoSVG-Multicolor-Printing) by goblinhanyikan (MIT). Keep the original attribution if you redistribute this tool.
