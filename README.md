# LENTICULAR CARD STUDIO

v1.0 · [Open the tool](https://demi-tequila.github.io/lenticular-card-studio/) · [中文说明](README.zh-CN.md)

One `index.html`. Drop a photo, get a card that looks like a photographed lenticular print, export a PNG. No build step, no account, no upload. The photo is read into Canvas and stays in the browser.

The starting point was a photographed lenticular card rebuilt by hand in a design file: six stacked layers, one of them a photo. Rebuilding those layers for every new photo is the part this tool removes.

## Run it

Double-click `index.html`. `file://` works. If your browser blocks clipboard access on local files, serve the folder instead:

    python3 -m http.server 8000

`?demo=1` loads a generated demo image, so you can see the material stack without finding a photo first. `?lang=zh` opens the Chinese UI. The language button remembers your choice; English is the default.

## What gets drawn

In order, on a 420 × 600 card:

1. The photo, cropped to fill. If only one photo is loaded, the B view is derived from it with a small crop shift plus a saturation / hue / brightness offset.
2. A/B interlace: vertical bands, alternating by viewing angle. Pointer position over the card (or phone tilt) drives the mix.
3. A lens gradient inside each band — white on one edge, black on the other. This is what reads as a cylindrical lens on screen.
4. A seven-stop rainbow gradient in `screen` blend mode, shifted slightly with the tilt.
5. A moving specular highlight, top and bottom shading, a thin gold frame.
6. Card text: title, rarity, serial. Editable in the left panel.

The print export skips layers 4–6 and uses a fixed 50/50 interlace. It is a print sheet, not a preview.

## Parameters

| Control | Range | Default | What it changes |
| --- | --- | --- | --- |
| Pitch (LPI) | 12–100 | 40 | Number of lens bands across the 404 px image area. Higher is finer and harder to align on a physical lens sheet. |
| Flip angle | 0–45° | 18° | How quickly A gives way to B as the pointer moves. |
| Parallax depth | 0–1 | 0.40 | Size of the crop shift and colour offset used to derive B. |
| Foil | 0–1 | 0.85 | Rainbow strength. Skipped in two-image mode. |
| Lens contrast | 0–1 | 0.22 | Dark/light contrast inside each band. |

Three modes are presets over the same sliders, not separate renderers: `Photo replica` (40 / 18 / .40 / .85 / .22), `Two-image lenticular` (60 / 24 / .60 / .12 / .34), `Single-image holographic` (40 / 12 / .25 / 1 / .10). The shipped JSON files are in `presets/`.

## Sharing and presets

`Copy share link` writes the entire state into the URL hash:

    #lang=en&mode=replica&lpi=40&flip=18&depth=0.40&foil=0.85&stripe=0.22&scale=2&title=...

The recipient opens the link and drops their own photo. Photos are deliberately left out — a data URL would make the link megabytes long and would upload someone's picture into a chat window. `Import preset` / `Export preset (.json)` carry the same state as a file, which is easier to paste into an issue or a discussion.

## Printing

Match the physical lenticular sheet to the LPI you exported. 40 LPI is commonly a ~0.635 mm pitch lens. The print export is 840 × 1200 px at 2×, with no foil, no frame and no text; you print it at the intended physical size and laminate the lens on top. Alignment tolerance is small — test a narrow strip before committing a full sheet. The screen preview is not a print proof.

## Known limits

- Real depth needs two photos or a depth map. The derived B view is a shifted copy of A, so parallax is an illusion, not geometry. Load a second photo for a real two-image flip.
- iOS requires a user gesture before `deviceorientation` works. Tap the A/B bar once; if permission is denied, pointer tilt still works on desktop.
- The B view uses Canvas `filter`. Older browsers without it get the crop shift without the colour offset.
- At 100 LPI and 3× export the render is slow on phones.
- Light and dark themes change the UI, not the card artwork.

## Community

Discussions is for cards and presets: post an exported PNG together with its share link, or a `.json` preset. If you tested a real lens sheet, say which LPI, which sheet and what printer — those notes are more useful than another screenshot. Issues are for broken rendering; please include the browser version and the share link from `Copy share link`, which usually reproduces the state on its own.

Adding a preset is one file in `presets/` and a PR. No code change needed. See [CONTRIBUTING.md](CONTRIBUTING.md).

## Where the numbers came from

The parameter vocabulary (pitch, stripe, flip angle, depth, foil) comes from [EverettFish/holo-card-studio](https://github.com/EverettFish/holo-card-studio) and the `interlacer.py` in [Cafe-lentique](https://github.com/Cafe-lentique). [fopina/lenticular.py](https://github.com/fopina/lenticular.py) was useful for the interlace math. No code from those projects is included here — this file is plain Canvas, and it runs with one photo instead of two.

## Files

    index.html            the whole app
    presets/              three shipped parameter files
    .github/              issue templates and a syntax check
    README.zh-CN.md       Chinese notes

MIT, see [LICENSE](LICENSE).
