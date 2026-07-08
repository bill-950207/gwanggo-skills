---
name: gwanggo-social-video
description: >
  Create short-form vertical videos for social media (TikTok, Reels, Shorts) with
  gwanggo AI. Use when the user says "short-form video", "vertical video", "TikTok/Reels/
  Shorts", "social video", "9:16 video", or wants a punchy clip for social feeds.
  Uses Seedance 2.0 with vertical framing and motion/audio guidance.
---

# gwanggo-social-video

Make a vertical (9:16) short clip tuned for social feeds.

## Setup

Needs `GWANGGO_API_KEY` (https://gwanggo.jocoding.io/dashboard/api-keys).
API basics (base URL, polling) are in the `gwanggo-generate` skill.

## Workflow

1. **Nail the hook**: the first second must grab attention — motion, reveal, or a
   surprising subject. Write that into the prompt.
2. **Optionally start from an image**: if the user has a product/keyframe image, pass it
   as `imageUrl` (image-to-video) for a controlled first frame. Otherwise text-to-video.
3. **Generate** with `seedance-2.0`, `aspectRatio:"9:16"`, `resolution:"720p"`,
   `duration:5` (or `10`), `generateAudio:true`.
4. **Poll** `GET /tasks/{id}` until `COMPLETED` → `result_url`.

### Generate (text-to-video)
```bash
curl -s -X POST https://gwanggo.jocoding.io/api/v1/generate/video \
  -H "Authorization: Bearer $GWANGGO_API_KEY" -H "Content-Type: application/json" \
  -d '{"model":"seedance-2.0","prompt":"<hook + scene + motion>","aspectRatio":"9:16","resolution":"720p","duration":5,"generateAudio":true}'
```

### Generate (image-to-video)
Add `"imageUrl":"https://.../first-frame.png"` to control the opening frame.

### Prompt recipe
```
<attention hook in first beat>, <subject + action>, <camera move: quick push-in /
orbit / whip>, <energetic mood>, vertical 9:16, social-ready
```
Example:
```
a kraken erupting from the sea and seizing an aircraft carrier, fast dramatic push-in,
stormy cinematic mood, vertical 9:16, social-ready
```

## Tips

- Keep it short (5s) for feed retention; go 10s only when the story needs it.
- Vertical 9:16 fills the phone screen — frame the subject centered and large.
- `generateAudio:true` adds a soundtrack; mute in the editor if you'll add your own.
- Video takes minutes — keep polling `GET /tasks/{id}`. Failures auto-refund credits.
