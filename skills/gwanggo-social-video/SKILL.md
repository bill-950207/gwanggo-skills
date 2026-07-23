---
name: gwanggo-social-video
description: >
  Create short-form vertical videos for TikTok, Reels, and Shorts with gwanggo AI
  through remote MCP or the gwanggo CLI fallback. Use for short-form or 9:16 video,
  image-to-video from an attached keyframe, campaign hook variants, generated audio,
  or reusing a prior generation. Plans exact credits before batches or reuse and polls
  long-running video jobs safely.
---

# gwanggo-social-video

Create a vertical, mobile-first short with a strong first-second hook. Prefer remote MCP
when connected; use the CLI only as a fallback. Never submit the same job through both
paths.

## Plan the short

1. Define the audience, platform, offer or story goal, source asset, and desired audio.
2. Put the hook in the first second: immediate motion, reveal, contrast, or surprise.
3. Keep one readable subject centered within mobile-safe framing.
4. Prefer 5 seconds for a single beat. Use longer duration only when the model supports
   it and the story needs it.

Prompt recipe:

```text
<first-second hook>, <subject and action>, <camera movement>, <visual progression>,
<energy and mood>, keep subject readable in vertical 9:16, social-ready
```

## Generate with remote MCP

1. Call `list_models` with `type: "video"`. Use the returned slug and supported aspect
   ratio, duration, resolution, audio, and default values. Prefer `seedance-2.0`,
   `9:16`, 720p, and 5 seconds when available and suitable.
2. Resolve an attached keyframe with the image workflow below.
3. Call `plan_generation` with the final prompt and options.
4. Check exact credits and balance. One explicit request for one video authorizes that
   planned submission. Ask before adding more outputs or expanding the scope.
5. Call `generate_video`. Pass `generate_audio: true` only when the user wants audio
   and the selected model supports it.
6. Poll the returned id with `get_task` until completion or failure. Video can take
   minutes; never duplicate the request because polling is slow.

## Generate hook variants

1. Write 1-4 complete prompts that vary the opening beat or creative direction.
2. Call `plan_generation` with the shared settings and matching `count`.
3. Show model, count, per-output credits, total credits, and available balance. Wait
   for explicit approval.
4. Call `generate_variants` with the exact `confirmed_total_credits`.
5. Poll each submitted id with `get_task`.

If the confirmed value becomes stale, re-plan and ask again. Never guess or bypass it.

## Use an attached keyframe

Never ask the user to host an attached or local image.

- ChatGPT: pass the native file object as `image_file`.
- Claude Code, Codex, Cursor, or another client with local-file access:
  1. Determine its JPEG, PNG, or WebP MIME type and byte size.
  2. Call `prepare_image_upload`.
  3. HTTP `PUT` the file bytes to `upload.url` using the exact `upload.headers`.
  4. Pass the returned `public_url` as `image`.
- Existing public keyframe: pass its HTTPS URL as `image`.

The upload limit is 20 MB and the signed target expires after five minutes. Do not
print or persist the signed URL. Use `end_image` or `end_image_file` only for a model
that supports an end frame.

## Reuse a previous short

1. Find it with `list_generations` and inspect it with `get_generation`.
2. Call `plan_generation` using the stored prompt and settings plus requested changes.
3. Show the exact cost and wait for approval.
4. Call `reuse_generation` with the exact `confirmed_total_credits`, then poll with
   `get_task`.

Use the MCP `social_short` prompt when available. Use `campaign_variants` when the user
wants multiple hooks or creative directions.

## Quality rules

- Describe motion, timing, and camera behavior; do not merely restate the keyframe.
- Avoid tiny subjects, slow empty openings, and important detail near screen edges.
- Keep product identity and on-pack text stable when animating an ad still.
- Treat generated dialogue or audio as optional unless explicitly requested.
- Failed jobs refund automatically; a slow job is not a failed job.

## CLI fallback

Run `gwanggo me`, then:

```bash
gwanggo generate video "<hook, scene, and camera motion>" \
  --model seedance-2.0 --aspect-ratio 9:16 --resolution 720p --duration 5 \
  --generate-audio
```

For image-to-video from an existing public image, add
`--image-url "https://.../first-frame.png"`. If the CLI or login is missing, follow
the setup in `gwanggo-generate`. Check a slow job with `gwanggo task <id>`.
