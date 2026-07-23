---
name: gwanggo-product-ad
description: >
  Create polished product advertisement images and videos with gwanggo AI through
  remote MCP or the gwanggo CLI fallback. Use for product ads, hero shots, lifestyle
  scenes, e-commerce images, campaign variants, product-photo attachments, saved
  product reuse, or animating a selected product image. Plans exact credit cost before
  batches or reuse and preserves real product identity from reference images.
---

# gwanggo-product-ad

Turn a product photo, saved gwanggo product, or description into an ad-quality image,
then optionally animate the selected result. Prefer remote MCP whenever connected; use
the CLI only as a fallback. Do not submit the same job through both paths.

## Gather product context

1. Identify the product, category, brand, selling point, target customer, platform, and
   intended mood.
2. If the user refers to a saved product, call `list_products`, choose the intended
   item, then call `get_product`. Reuse its product images and factual brand context.
3. If the user attaches or pastes a photo, prepare it with the attachment workflow
   below. Never ask them to host it or provide a URL.
4. Preserve the product's shape, label, packaging, colors, and recognizable details
   unless the user explicitly requests a redesign.

## Create a hero image

1. Call `list_models` with `type: "image"` and select a model from the returned slugs
   and options. Prefer `gpt-image-2` at high quality when available and no other model
   is requested.
2. Compose a production prompt:

```text
<product and identity constraints>, <material and texture>, on <surface> in <setting>,
<lighting>, <camera and composition>, <brand mood>, <platform placement>,
commercial product photography, readable packaging, no competing products
```

3. Call `plan_generation` with the final prompt, options, and output count. Check the
   exact cost and balance.
4. For one explicitly requested image, proceed with `generate_image`. For 2-4
   directions, show the model, count, per-output cost, total cost, and balance; wait
   for explicit approval, then call `generate_variants` with the exact
   `confirmed_total_credits`.
5. Poll submitted ids with `get_task`; never duplicate a slow job.
6. Present the results as distinct creative directions and let the user choose before
   animating.

## Animate the selected image

1. Use the chosen result URL as `image`.
2. Call `list_models` with `type: "video"` and choose a compatible image-to-video
   model. Prefer `seedance-2.0`, 720p, and 5 seconds when available and suitable.
3. Write a motion-first prompt, for example:

```text
slow controlled push-in, subtle light sweep across the product, restrained particles,
keep the package and label stable, premium commercial finish
```

4. Call `plan_generation` and show the exact video cost. If animation was not part of
   the user's explicit request, wait for approval before spending credits.
5. Call `generate_video`, then poll with `get_task`.

## Use attached product images

- ChatGPT: pass the native attachment as `image_file`.
- Claude Code, Codex, Cursor, or another local-file client:
  1. Detect JPEG, PNG, or WebP MIME type and size.
  2. Call `prepare_image_upload`.
  3. HTTP `PUT` the file bytes to `upload.url` with the exact `upload.headers`.
  4. Pass `public_url` as `image`.
- Existing public image: pass its HTTPS URL as `image`.

Uploads support JPEG, PNG, and WebP up to 20 MB. Signed targets expire after five
minutes. Do not expose the signed upload URL. Use `end_image` or `end_image_file` only
when the selected video model supports an end frame.

## Reuse and revise

1. Use `list_generations` and `get_generation` to inspect previous product results.
2. To regenerate one, run `plan_generation` with its stored values and requested
   overrides.
3. Show the exact current cost and wait for approval.
4. Call `reuse_generation` with the exact `confirmed_total_credits`.

Use the MCP `product_ad` prompt when the client exposes server prompts. Recent saved
products and generations are also available as MCP resources.

## Quality rules

- Keep one clear hero product and remove background clutter.
- Match the light direction and contact shadow to the new environment.
- Reserve negative space intentionally for copy; do not generate marketing text unless
  requested.
- Do not invent product claims, prices, certifications, ingredients, or brand facts.
- Generate extra variations only after the user approves the planned total credits.
- Failed generation jobs are automatically refunded.

## CLI fallback

Run `gwanggo me`, then use:

```bash
gwanggo generate image "<hero prompt>" \
  --model gpt-image-2 --aspect-ratio 1:1 --quality high
```

For a public product photo, add `--image-url "https://.../product.png"`.

Animate the selected public result:

```bash
gwanggo generate video "slow push-in, gentle light sweep across the product" \
  --model seedance-2.0 --image-url "<hero result URL>" \
  --resolution 720p --duration 5 --aspect-ratio 16:9 --generate-audio
```

If the CLI or login is missing, follow the setup in `gwanggo-generate`. Check slow jobs
with `gwanggo task <id>` instead of submitting again.
