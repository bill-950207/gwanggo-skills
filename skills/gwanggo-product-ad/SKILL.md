---
name: gwanggo-product-ad
description: >
  Create polished product advertisement visuals with gwanggo AI — hero product shots,
  lifestyle scenes, e-commerce images, and short product videos. Use when the user says
  "product ad", "product photo", "hero shot", "e-commerce image", "advertise this
  product", "make an ad for <product>", or wants a marketing visual for a product.
  Builds a strong ad prompt, generates the image, then optionally animates it to video.
---

# gwanggo-product-ad

Turn a product (photo or description) into an ad-quality image, and optionally a video.

## Setup

Needs `GWANGGO_API_KEY` (get one at https://gwanggo.jocoding.io/dashboard/api-keys).
API basics (base URL, endpoints, polling) are in the `gwanggo-generate` skill — reuse them.

## Workflow

1. **Understand the product**: name, category, key selling point, target vibe
   (luxury / playful / natural / techy). If the user has a product image URL, pass it
   as `imageUrl` so the model preserves the real product.
2. **Compose the hero image prompt** using the recipe below.
3. **Generate the image** with `gpt-image-2`, `quality:"high"`. Default aspect `1:1`
   (marketplace) or `4:5` / `16:9` if the user names a placement.
4. **Poll** `GET /tasks/{id}` for the `result_url`.
5. **(Optional) Animate to video**: feed the resulting image as `imageUrl` to
   `seedance-2.0` with a subtle motion prompt (see gwanggo-social-video for vertical).

### Hero image prompt recipe

```
<product>, <material/texture>, centered on <surface> in <environment>,
<lighting: soft studio / golden morning / dramatic side>, <mood>,
shallow depth of field, high detail, commercial product photography, 8k
```

Example:
```
a frosted glass perfume bottle, on wet river stones, soft morning sunlight,
calm and premium mood, shallow depth of field, commercial product photography, 8k
```

### Generate
```bash
curl -s -X POST https://gwanggo.jocoding.io/api/v1/generate/image \
  -H "Authorization: Bearer $GWANGGO_API_KEY" -H "Content-Type: application/json" \
  -d '{"model":"gpt-image-2","prompt":"<hero prompt>","aspectRatio":"1:1","quality":"high"}'
```
With a real product photo, add `"imageUrl":"https://.../product.png"`.

### Animate the winner (optional)
```bash
curl -s -X POST https://gwanggo.jocoding.io/api/v1/generate/video \
  -H "Authorization: Bearer $GWANGGO_API_KEY" -H "Content-Type: application/json" \
  -d '{"model":"seedance-2.0","prompt":"slow push-in, gentle light sweep across the product","imageUrl":"<hero result_url>","resolution":"720p","duration":5,"aspectRatio":"16:9","generateAudio":true}'
```

## Tips

- Keep the product the hero: one clear subject, uncluttered background.
- Match lighting to the brand mood; avoid busy scenes that hide the product.
- Generate 2–3 variations and let the user pick before animating.
- Credits are auto-refunded on failure.
