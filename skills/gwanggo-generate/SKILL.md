---
name: gwanggo-generate
description: >
  Generate, edit, reuse, and manage advertising images and videos with gwanggo AI
  through the remote MCP server or the gwanggo CLI fallback. Use when the user asks
  to generate an image or video, attach or paste an image for image-to-image or
  image-to-video, create variants, reuse generation history, inspect saved products,
  estimate generation credits, or check a generation task. Supports GPT Image 2,
  Seedream 5, Nano Banana, Seedance 2.0, Kling 3, Veo 3.1, Hailuo, and more.
---

# gwanggo-generate

Use gwanggo's remote MCP tools when they are connected. They support attachment upload,
cost planning, history, saved products, variants, and reuse. Use the CLI only as a
fallback. Never submit the same job through both paths.

## Choose the execution path

1. Use remote MCP when `list_models`, `plan_generation`, and the generation tools are
   available.
2. Otherwise use the CLI workflow below.
3. If neither path is authenticated, guide the user through one login path; never ask
   them to paste or export an API key.

Remote MCP URL:

```text
https://gwanggo.ai/api/v1/mcp
```

Claude Code setup:

```bash
claude mcp add --transport http --scope user --callback-port 3118 gwanggo https://gwanggo.ai/api/v1/mcp
```

Codex setup:

```bash
codex mcp add gwanggo --url https://gwanggo.ai/api/v1/mcp
codex mcp login gwanggo
```

## Remote MCP workflow

### Generate one image or video

1. Call `list_models` for the required media type. Use its model slug, options,
   defaults, and credit configuration; do not invent unsupported values.
2. Resolve any reference image with the attachment workflow below.
3. Call `plan_generation` with the final model, prompt, options, and `count: 1`.
4. Check `total_credits`, `available_credits`, `affordable`, and resolved defaults.
   An explicit request to generate one output authorizes that one planned submission.
   Ask before increasing the output count or materially changing the requested scope.
5. Call `generate_image` or `generate_video`.
6. If the result is still submitted or processing, poll its id with `get_task`. Never
   resubmit merely because generation is slow.
7. Return the completed media link and summarize the model and credits used.

### Generate variants

1. Write one complete prompt per variant, with 1-4 prompts total.
2. Call `plan_generation` using the same model/options and `count` equal to the number
   of prompts.
3. Show the user the model, count, per-output credits, total credits, and available
   balance. Wait for explicit approval.
4. Call `generate_variants` only after approval, passing
   `confirmed_total_credits` exactly equal to the latest plan's `total_credits`.
5. Poll every submitted id with `get_task`.

If the confirmation value is rejected, call `plan_generation` again and show the new
cost. Never guess, round, or bypass the value.

### Reuse a previous generation

1. Find the item with `list_generations`, then inspect it with `get_generation`.
2. Call `plan_generation` using the stored model, prompt, and parameters plus any
   requested overrides.
3. Show the exact current cost and wait for approval.
4. Call `reuse_generation` with the source id and the exact
   `confirmed_total_credits`.
5. Poll with `get_task`.

### Use attached, pasted, or local images

Never ask the user to upload an attached image elsewhere or provide a public URL.

- ChatGPT native attachment: pass the host-provided file object as `image_file`. For an
  end frame, use `end_image_file`.
- Claude Code, Codex, Cursor, or another client with a readable local file:
  1. Determine the real MIME type and byte size.
  2. Call `prepare_image_upload` with `content_type`, `file_name`, and `size_bytes`.
  3. Upload the exact file bytes with HTTP `PUT` to `upload.url`, using the exact
     `upload.headers`.
  4. Pass the returned `public_url` as `image` or `end_image`.
- Existing public image: pass its HTTPS URL as `image`.
- Small inline image: pass a valid `data:image/...;base64,...` value as `image` only
  when it is at most 3 MB.

Accepted upload types are JPEG, PNG, and WebP, up to 20 MB. The signed upload URL
expires after five minutes. Do not print or persist the signed URL.

Example local upload after `prepare_image_upload`:

```bash
curl --fail-with-body --request PUT \
  --header "Content-Type: image/png" \
  --upload-file "/absolute/path/reference.png" \
  "<upload.url>"
```

### Reuse account context

- Use `list_generations` and `get_generation` instead of asking the user to find an old
  result URL.
- Use `list_products` and `get_product` to retrieve saved product images, brand,
  description, price, and selling points.
- MCP resources expose recent items as `gwanggo://generation/<id>` and
  `gwanggo://product/<id>`.
- MCP prompts include `product_ad`, `social_short`, and `campaign_variants`.

## MCP tool map

- Discovery: `list_models`, `get_credits`
- History: `list_generations`, `get_generation`
- Products: `list_products`, `get_product`
- Safe planning and attachment: `plan_generation`, `prepare_image_upload`
- Generation: `generate_image`, `generate_video`, `generate_variants`,
  `reuse_generation`
- Status: `get_task`

## Prompt guidance

- Specify subject, setting, lighting, mood, lens or camera movement, and color palette.
- For ads, identify the product and the intended surface, placement, or platform.
- For image-to-video, describe motion and camera behavior rather than restating only
  the still image.

## CLI fallback

Check the CLI and saved browser login:

```bash
command -v gwanggo && gwanggo me
```

Install or authenticate when required:

```bash
npm install -g https://github.com/bill-950207/gwanggo-mcp/archive/refs/heads/master.tar.gz
gwanggo auth login
```

List models and generate:

```bash
gwanggo models
gwanggo generate image "<prompt>" \
  --model gpt-image-2 --aspect-ratio 1:1 --quality high
gwanggo generate video "<scene and motion>" \
  --model seedance-2.0 --resolution 720p --duration 5 --aspect-ratio 16:9
```

For a public reference image, add `--image-url "https://.../image.png"`. Check a slow
task with `gwanggo task <id>`. Credits are charged on submission and failed jobs are
automatically refunded.
