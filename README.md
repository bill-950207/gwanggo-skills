# gwanggo skills

Agent skills for generating advertising **images and videos** with [gwanggo AI](https://gwanggo.jocoding.io) — GPT Image 2, Seedream 5, Nano Banana, Seedance 2.0, Kling 3, Veo 3.1, and 16+ more models — from any coding agent (Claude Code, Cursor, Codex, …).

## Install

```bash
npx skills add bill-950207/gwanggo-skills
```

Then set your API key (get one at https://gwanggo.jocoding.io/dashboard/api-keys):

```bash
export GWANGGO_API_KEY=gwk_xxxxxxxx
```

Now ask your agent, or invoke a skill directly:

```
/gwanggo-generate
```

## Skills

| Skill | What it does |
|-------|--------------|
| **gwanggo-generate** | General image & video generation. Pick a model, submit, poll for the result. |
| **gwanggo-product-ad** | Product advertisement recipe — hero product image, optionally animated to video. |
| **gwanggo-social-video** | Short-form vertical (9:16) video for TikTok / Reels / Shorts. |

## How it works

The skills call the gwanggo REST API (`https://gwanggo.jocoding.io/api/v1`) with your
`GWANGGO_API_KEY`. Credits are charged on submit and **auto-refunded** if a job fails.

Prefer a hosted connection with no key? gwanggo also runs a **remote MCP server** —
add `https://gwanggo.jocoding.io/api/v1/mcp` as a custom connector in Claude or ChatGPT
and sign in. See https://gwanggo.jocoding.io/dashboard/mcp

## License

MIT
