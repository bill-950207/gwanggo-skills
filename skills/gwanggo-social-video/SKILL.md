---
name: gwanggo-social-video
description: >
  Create short-form vertical videos for social media (TikTok, Reels, Shorts) with
  gwanggo AI. Use when the user says "short-form video", "vertical video", "TikTok/Reels/
  Shorts", "social video", "9:16 video", or wants a punchy clip for social feeds.
  Uses the gwanggo CLI and its saved browser login with Seedance 2.0 vertical framing
  and motion/audio guidance.
---

# gwanggo-social-video

Make a vertical 9:16 short clip for social feeds.

## Setup

Use the installed `gwanggo` CLI. Run `gwanggo me` first. If authentication is missing,
ask the user to run `gwanggo auth login` and approve the browser prompt. Do not request a
separate API key. See `gwanggo-generate` for CLI installation and general model guidance.

## Workflow

1. Put the hook in the first second: motion, reveal, or a surprising subject.
2. If the user provides a keyframe URL, pass it with `--image-url`.
3. Generate with `seedance-2.0`, aspect ratio `9:16`, resolution `720p`, and duration
   `5` unless the user needs a longer story.
4. Add `--generate-audio` when the user wants generated audio.
5. Return the final URL printed by the CLI. Do not submit a duplicate while polling.

### Generate text-to-video

```bash
gwanggo generate video "<hook, scene, and camera motion>" \
  --model seedance-2.0 --aspect-ratio 9:16 --resolution 720p --duration 5 \
  --generate-audio
```

For image-to-video, add `--image-url "https://.../first-frame.png"`.

### Prompt recipe

```text
<attention hook in first beat>, <subject and action>,
<camera move>, <energetic mood>, vertical 9:16, social-ready
```

Example:

```text
a kraken erupting from the sea and seizing an aircraft carrier,
fast dramatic push-in, stormy cinematic mood, vertical 9:16, social-ready
```

## Tips

- Prefer 5 seconds for feed retention; use 10 seconds only when needed.
- Keep the subject centered and large enough for a phone screen.
- Generated audio can be removed later if the editor will add a soundtrack.
- Video generation can take minutes; let the CLI poll. Failed jobs refund automatically.
