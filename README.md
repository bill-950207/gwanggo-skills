# gwanggo skills

Agent skills for planning, generating, reusing, and managing advertising **images and
videos** with [gwanggo AI](https://gwanggo.ai) — GPT Image 2, Seedream 5, Nano Banana,
Seedance 2.0, Kling 3, Veo 3.1, and more — from Claude Code, Claude Desktop, ChatGPT,
Cursor, Codex, and other MCP or Agent Skills clients.

## Install

```bash
npm install -g https://github.com/bill-950207/gwanggo-mcp/archive/refs/heads/master.tar.gz
gwanggo auth login
npx skills add bill-950207/gwanggo-skills
```

Approve the browser prompt once. The CLI skills reuse the saved login; no API-key
environment variable is required. Ask your agent, or invoke a skill directly:

```
/gwanggo-generate
```

## Skills

| Skill | What it does |
|-------|--------------|
| **gwanggo-generate** | Images and videos with cost planning, attachments, history, variants, reuse, and task polling. |
| **gwanggo-product-ad** | Product hero images, saved-product context, campaign variants, and image-to-video ads. |
| **gwanggo-social-video** | Short-form vertical video, attached keyframes, hook variants, audio, and safe polling. |

## Recommended: remote MCP

The skills prefer gwanggo's remote MCP server when it is connected and fall back to the
local CLI otherwise. Remote MCP supports OAuth, local or native image attachments,
exact credit planning, generation history, saved products, batches, and reuse.

```text
https://gwanggo.ai/api/v1/mcp
```

Claude Code:

```bash
claude mcp add --transport http --scope user --callback-port 3118 gwanggo https://gwanggo.ai/api/v1/mcp
```

Codex:

```bash
codex mcp add gwanggo --url https://gwanggo.ai/api/v1/mcp
codex mcp login gwanggo
```

For Claude Desktop or ChatGPT, add the URL as a custom connector and approve OAuth in
the browser. See [gwanggo MCP setup](https://gwanggo.ai/dashboard/mcp).

## MCP capabilities

- Models and account: `list_models`, `get_credits`
- History and products: `list_generations`, `get_generation`, `list_products`,
  `get_product`
- Planning and attachments: `plan_generation`, `prepare_image_upload`
- Creation and reuse: `generate_image`, `generate_video`, `generate_variants`,
  `reuse_generation`
- Status: `get_task`

Batch and reuse flows require an exact planned credit confirmation. Failed generation
jobs are automatically refunded.

## CLI fallback

The CLI uses the account saved by `gwanggo auth login`, submits the job, and polls for
the result. Use it when remote MCP tools are unavailable.

## License

MIT
