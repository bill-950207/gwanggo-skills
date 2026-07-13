---
name: gwanggo-product-ad
description: >
  Create polished product advertisement visuals with gwanggo AI — hero product shots,
  lifestyle scenes, e-commerce images, and short product videos. Use when the user says
  "product ad", "product photo", "hero shot", "e-commerce image", "advertise this
  product", "make an ad for <product>", or wants a marketing visual for a product.
  Builds a strong ad prompt, generates the image through the gwanggo CLI, then optionally
  animates it to video using the same saved login.
---

# gwanggo-product-ad

Turn a product photo or description into an ad-quality image, then optionally animate it.

## Setup

Use the installed `gwanggo` CLI. Run `gwanggo me` first. If authentication is missing,
ask the user to run `gwanggo auth login` and approve the browser prompt. Do not request a
separate API key. See `gwanggo-generate` for CLI installation and general model guidance.

## Workflow

1. Identify the product, category, selling point, and target mood.
2. If the user provides a product image URL, preserve it with `--image-url`.
3. Compose the hero prompt using the recipe below.
4. Generate with `gpt-image-2`, `--quality high`, and the requested placement ratio.
5. Return the result URL printed by the CLI.
6. Animate only after the user asks or selects a result.

### Hero image prompt recipe

```text
<product>, <material/texture>, centered on <surface> in <environment>,
<lighting>, <mood>, shallow depth of field, high detail,
commercial product photography
```

Example:

```text
a frosted glass perfume bottle on wet river stones, soft morning sunlight,
calm premium mood, shallow depth of field, commercial product photography
```

### Generate

```bash
gwanggo generate image "<hero prompt>" \
  --model gpt-image-2 --aspect-ratio 1:1 --quality high
```

For a real product photo, add `--image-url "https://.../product.png"`.

### Animate the selected image

```bash
gwanggo generate video "slow push-in, gentle light sweep across the product" \
  --model seedance-2.0 --image-url "<hero result URL>" \
  --resolution 720p --duration 5 --aspect-ratio 16:9 --generate-audio
```

## Tips

- Keep one clear product as the hero and avoid busy backgrounds.
- Match lighting to the brand mood.
- Generate multiple variations only when the user requests them; every submission spends credits.
- Failed jobs are refunded automatically. Do not resubmit while the original job is polling.
