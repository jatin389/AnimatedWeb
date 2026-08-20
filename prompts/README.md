# Ambre Noir — manual asset handoff

Architecture **A** (one continuous forward walkthrough), N=4, desktop only.
No connectors — the four legs *are* the journey.

## Hard rule

Every leg after the first must **start on the previous leg's actual last frame**.
Your video tool must accept a start/first frame. Do not generate a leg from a still
except for leg 1.

## 1. Stills (4) — any image tool

3:2 landscape, ≥1536px wide, no text. Same tool for all four (mixing tools reads as style drift).

| Prompt file | Save as | Status |
|---|---|---|
| `still_1_fields.txt` | `assets/img/still_1_fields.png` | accepted |
| `still_2_distillery.txt` | `assets/img/still_2_distillery.png` | **re-export — watermark still present** |
| `still_3_atelier.txt` | `assets/img/still_3_atelier.png` | accepted |
| `still_4_hero.txt` | `assets/img/still_4_hero.png` | accepted |

Watermark check (luminance in the x1160–1264 / y745–840 corner; a clean still reads
max < ~80 with zero pixels over 120):

```bash
ffmpeg -v error -i assets/img/still_2_distillery.png -vf "crop=104:95:1160:745,format=gray" -f rawvideo - \
 | python3 -c "import sys;d=sys.stdin.buffer.read();print('max',max(d),'over120',sum(1 for p in d if p>120))"
```

Stills 2–4 serve only as poster/reduced-motion fallbacks; the legs derive their content
from the chain, not from these files.

## 2. Legs (4) — sequential, video tool with start-frame conditioning

16:9, ~8s, highest quality, no audio.

| Prompt file | Start frame | Save as | Status |
|---|---|---|---|
| `leg_1_fields.txt` | `assets/img/still_1_fields.png` | `assets/vid/leg_1_fields.mp4` | pending |
| `leg_2_distillery.txt` | last frame of `leg_1_fields.mp4` | `assets/vid/leg_2_distillery.mp4` | pending |
| `leg_3_atelier.txt` | last frame of `leg_2_distillery.mp4` | `assets/vid/leg_3_atelier.mp4` | pending |
| `leg_4_hero.txt` | last frame of `leg_3_atelier.mp4` | `assets/vid/leg_4_hero.mp4` | pending |

Extract each leg's last frame before generating the next:

```bash
ffmpeg -sseof -0.15 -i assets/vid/leg_1_fields.mp4 -frames:v 1 -q:v 2 last_1.png
```

Before chaining, eyeball that last frame: it must read as a frame from a calm forward
glide. A bad handoff frame poisons every leg after it — re-roll instead.

## 3. Encode for scrubbing

```bash
ffmpeg -i raw.mp4 -an -vf "unsharp=5:5:0.8:5:5:0.0" -c:v libx264 -preset slow -crf 20 \
  -pix_fmt yuv420p -g 8 -keyint_min 8 -sc_threshold 0 -movflags +faststart assets/vid/leg_1_fields.mp4
```

## 4. Serve

```bash
python3 -m http.server 8912
```
