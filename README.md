# AnimatedWeb — Ambre Noir

A scroll-scrubbed "fly through the world" landing page for a fictional luxury fragrance
brand, built with the [lets-scroll](https://github.com/AIwithhassan/lets-scroll) skill.

Scroll drives a camera through one continuous forward flight: dawn harvest → distillation
hall → perfumer's atelier → the bottle. Scroll only drives time; the camera motion is
pre-rendered.

## Run it

```bash
python3 -m http.server 8912
```

Then open http://localhost:8912.

## Status

Scaffold complete, video chain not yet rendered — the page currently shows the scene
stills as posters. See [prompts/README.md](prompts/README.md) for the asset handoff and
the render/verify/encode steps.

| Asset | State |
|---|---|
| Scene stills 1, 3, 4 | done |
| Scene still 2 | needs re-export (AI-provenance watermark still present) |
| Legs 1–4 (video) | not started — strictly sequential, each starts on the previous leg's last frame |

## Layout

- `index.html` — page + engine config (sections, copy, pacing, dark theme)
- `scrub-engine.js` — the scroll-scrub engine (vendored, unmodified)
- `prompts/` — style preamble and every still/leg prompt, plus the handoff instructions
- `assets/img/` — scene stills (video posters + reduced-motion fallbacks)
- `assets/vid/` — encoded leg clips (not yet present)
