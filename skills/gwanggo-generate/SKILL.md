---
name: gwanggo-generate
description: >
  Generate advertising images and videos with gwanggo AI (16+ models: GPT Image 2,
  Seedream 5, Nano Banana, Seedance 2.0, Kling 3, Veo 3.1, Hailuo, and more).
  Use when the user says "generate an image", "make a video", "create an ad visual",
  "image to video", "animate this photo", "make a product shot", or asks for any
  AI image/video generation. Uses the gwanggo CLI and its saved browser login; spends
  credits; failed jobs are auto-refunded.
---

# gwanggo-generate

Generate images and videos with the installed `gwanggo` CLI. The CLI submits the job,
polls until it finishes, and prints the result URL.

## Setup

Check the CLI and saved login before the first generation:

```bash
command -v gwanggo && gwanggo me
```

If the CLI is missing, install it:

```bash
npm install -g https://github.com/bill-950207/gwanggo-mcp/archive/refs/heads/master.tar.gz
```

If `gwanggo me` reports that authentication is missing, ask the user to run this once
and approve the browser prompt:

```bash
gwanggo auth login
```

Do not request or export `GWANGGO_API_KEY` for an interactive login. The CLI reuses the
credential stored by `gwanggo auth login`.

## Defaults

- Image: `gpt-image-2`, aspect ratio `1:1`, quality `high`
- Video: `seedance-2.0`, resolution `720p`, duration `5`, aspect ratio `16:9`

Only change models when the user asks or the task clearly needs another one. Run
`gwanggo models` when unsure which model slug to use.

## Workflow

1. Confirm authentication with `gwanggo me`.
2. Optionally run `gwanggo models` to choose a model.
3. Run one generation command. Keep the prompt in one quoted argument.
4. Return the final URL printed by the CLI. If polling times out, use the submitted id
   with `gwanggo task <id>`.

### List models

```bash
gwanggo models
```

### Generate an image

```bash
gwanggo generate image "<what to generate>" \
  --model gpt-image-2 --aspect-ratio 1:1 --quality high
```

For editing or image-to-image generation, add `--image-url "https://.../image.png"`.

### Generate a video

```bash
gwanggo generate video "<scene and motion>" \
  --model seedance-2.0 --resolution 720p --duration 5 --aspect-ratio 16:9
```

For image-to-video, add `--image-url "https://.../first-frame.png"`. Add
`--generate-audio` only when the user wants generated audio and the selected model
supports it.

### Check a task or credits

```bash
gwanggo task <id>
gwanggo me
```

## Prompt tips

- Be concrete: subject, setting, lighting, mood, lens/angle, and color palette.
- For ads, name the product clearly and the surface or context it sits in.
- Images are usually ready in seconds; videos can take minutes. Let the CLI keep polling.

## Notes

- Credits are charged on submit and auto-refunded if a job fails.
- Do not submit a duplicate job because polling appears slow.
- If the gwanggo remote MCP server is already connected, use its `generate_image`,
  `generate_video`, and `get_task` tools instead of invoking the CLI.
