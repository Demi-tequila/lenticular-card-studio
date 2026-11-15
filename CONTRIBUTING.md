# Contributing

The app is one file, `index.html`. Keep it that way unless there is a concrete reason not to.

## Where things live

- `paintBase()` — the A/B interlace and the viewing-angle mix.
- `paintLens()` — the per-band dark/light gradient.
- `paintFoil()` — the seven-stop rainbow layer.
- `paintSpecular()` / `paintShade()` / `paintFrame()` / `paintText()` — highlight, vignette, border, card copy.
- `layer()` — photo crop and the derived B view.
- `P`, `PRESETS`, `I18N` — state, mode defaults, and both languages.
- `syncHash()` / `applyState()` — share links and preset files. They must stay in sync: a field added to one has to be added to the other.

## Before a PR

1. Open `index.html` directly, then through `python3 -m http.server 8000`.
2. Check EN and 中文, light and dark.
3. Export one PNG and one print interlace.
4. Run the same check CI runs:

       python3 -c "import re; s=open('index.html').read(); open('/tmp/app.js','w').write(re.search(r'<script>([\s\S]*?)</script>',s).group(1))"
       node --check /tmp/app.js

5. If you changed a default, update the matching file in `presets/` and the table in `README.md`.

## Adding a preset

Copy a file from `presets/`, change the numbers, open a PR with a screenshot of the result. No code change needed. Please keep the `app` and `version` fields.

## Adding a language

Copy the `en` object inside `I18N`, add a button to `#langSeg`, and link the translation from the README. Do not fork `index.html` into a second HTML file — the two copies drift within a week.

## Things I would rather not merge

- npm, a bundler, or remote fonts. It runs offline by design.
- Photo uploads or analytics. The photo never leaves the browser; that is the whole privacy story.
- New render modes that only change slider values. Those are presets.

## Bug reports

Include the browser and version, and paste the link from `Copy share link`. That hash carries the settings and card text, so it usually reproduces the state without a long description. Say what you expected to see and what you actually saw; screenshots help when the Canvas itself is wrong.
