---
name: gwanggo-generate
description: >
  Generate advertising images and videos with gwanggo AI (16+ models: GPT Image 2,
  Seedream 5, Nano Banana, Seedance 2.0, Kling 3, Veo 3.1, Hailuo, and more).
  Use when the user says "generate an image", "make a video", "create an ad visual",
  "image to video", "animate this photo", "make a product shot", or asks for any
  AI image/video generation. Calls the gwanggo REST API; spends credits; failed jobs
  are auto-refunded.
---

# gwanggo-generate

Generate images and videos through the gwanggo API. Pick a model, submit a job, poll until done.

## Setup

Requires a gwanggo API key in the `GWANGGO_API_KEY` environment variable.
Get one at https://gwanggo.jocoding.io/dashboard/api-keys

```bash
export GWANGGO_API_KEY=gwk_xxxxxxxx
```

Base URL: `https://gwanggo.jocoding.io/api/v1` · Auth: `Authorization: Bearer $GWANGGO_API_KEY`

## Defaults

- Image → `gpt-image-2` (aspect_ratio `1:1`, quality `high`)
- Video → `seedance-2.0` (resolution `720p`, duration `5`, aspect_ratio `16:9`)

Only change models when the user asks or the task clearly needs another one. Run
`list_models` (below) when unsure which models/options exist.

## Workflow

1. (Optional) List models to choose a slug and see its options.
2. Submit the job. Note the returned `id`.
3. Poll `GET /tasks/{id}` every few seconds until `status` is `COMPLETED` (returns `result_url`) or `FAILED` (credits auto-refunded).

### List models
```bash
curl -s https://gwanggo.jocoding.io/api/v1/models \
  -H "Authorization: Bearer $GWANGGO_API_KEY" | jq '.models[] | {slug,name,type,creator}'
```

### Generate an image
```bash
curl -s -X POST https://gwanggo.jocoding.io/api/v1/generate/image \
  -H "Authorization: Bearer $GWANGGO_API_KEY" -H "Content-Type: application/json" \
  -d '{"model":"gpt-image-2","prompt":"<what to generate>","aspectRatio":"1:1","quality":"high"}'
# -> { "id": "...", "task_id": "...", "credits_used": 3 }
```
Optional image fields: `imageUrl` (reference for edit/i2i models), `aspectRatio`
(`1:1`,`16:9`,`9:16`,...), `quality` (`low`|`medium`|`high`, model-dependent).

### Generate a video
```bash
curl -s -X POST https://gwanggo.jocoding.io/api/v1/generate/video \
  -H "Authorization: Bearer $GWANGGO_API_KEY" -H "Content-Type: application/json" \
  -d '{"model":"seedance-2.0","prompt":"<scene/motion>","resolution":"720p","duration":5,"aspectRatio":"16:9"}'
```
seedance-2.0 fields: `resolution` (`480p`|`720p`) and `duration` (`5`|`10`) are
required; `aspectRatio` (`16:9`|`9:16`|`1:1`), `imageUrl` (image-to-video), and
`generateAudio` (bool) are optional. Other video models differ — check `list_models`.

### Poll for the result
```bash
curl -s https://gwanggo.jocoding.io/api/v1/tasks/<id> \
  -H "Authorization: Bearer $GWANGGO_API_KEY"
# -> { "status": "IN_PROGRESS" | "COMPLETED" | "FAILED", "result_url": "https://...", ... }
```

### Check credits
```bash
curl -s https://gwanggo.jocoding.io/api/v1/me -H "Authorization: Bearer $GWANGGO_API_KEY"
# -> { "email": "...", "credits": 240 }
```

## Prompt tips

- Be concrete: subject, setting, lighting, mood, lens/angle, color palette.
- For ads, name the product clearly and the surface/context it sits in.
- Images are usually ready in seconds; video takes minutes — keep polling.

## Notes

- Credits are charged on submit and **auto-refunded** if a job fails.
- If you have the gwanggo MCP connected instead, use its `generate_image` /
  `generate_video` / `get_task` tools — same models, no API key needed.
