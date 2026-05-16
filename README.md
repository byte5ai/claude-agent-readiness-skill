# Claude Agent Readiness Skill

A Claude skill that audits **websites and code repositories** for how well they are discoverable, fetchable, parsable, and citable by AI search systems (ChatGPT, Claude, Perplexity, Google AI Mode, Bing Copilot) and coding agents (Codex, Cursor, Aider, Junie, etc.).

The skill produces two outputs side by side: a **structured audit report** with score, findings, and prioritized actions, and an optional **sparring discussion** on the trade-offs behind the recommendations.

## What makes this skill different

Most GEO/AEO tooling on the market sells optimization without separating evidence from hype. This skill makes that separation explicit:

- Every recommendation carries a **tier tag** — `S` (platform primary sources), `A` (peer-reviewed academic research), `B` (empirical practitioner studies).
- Every recommendation carries a **confidence flag** — 🟢🟢 (method-sound, multi-tested, non-vendor-interest), 🟢 (solid finding), 🟡 (plausible but methodologically limited), 🔴 (marketing claim, anecdote, or no clear primary source).
- A mandatory **"What to skip" section** in every audit report names overhyped practices (Schema.org for AI citations, `/llms.txt` as a visibility lever, blanket date-bumping, programmatic SEO at scale) with the data that disproves them.

The knowledge base behind the skill is built on the GEO literature (Princeton KDD 2024, Toronto 2025, MIT/Columbia E-GEO, EMNLP 2025), platform documentation (OpenAI, Anthropic, Google, Bing, Vercel, Cloudflare), and large-scale empirical studies (notably the Ahrefs DiD study on Schema markup with n=1,885 + 4,000 matched controls, which shows no measurable Schema effect on AI citation rate).

## Installation

### Option 1 — Download the release bundle (recommended)

1. Download [`claude-agent-readiness-skill-v1.0.0.zip`](https://github.com/byte5ai/claude-agent-readiness-skill/releases/latest) from the latest release.
2. Unzip into your Claude skills directory:
   - Claude Code (macOS/Linux): `~/.claude/skills/`
   - Cowork mode: drop the unzipped folder into your skills location or use the plugin manager.
3. Restart Claude. The skill activates on any of the trigger phrases listed in `SKILL.md`.

That's it — three steps, no git, no clone.

### Option 2 — Clone the repository (for contributors and forks)

```bash
cd ~/.claude/skills/
git clone https://github.com/byte5ai/claude-agent-readiness-skill.git ai-agent-readiness
```

Same result as Option 1, but the folder stays a working git checkout so updates are a `git pull` away.

### Option 3 — Paste into a system prompt or project context

If you do not use a skill loader, the skill is plain Markdown. Paste the contents of `SKILL.md` and the relevant `assets/` files directly into a Claude project's system prompt or context. The tier and confidence rules still apply.

## How to use

Trigger the skill with any natural-language request that signals "audit a site for AI visibility", for example:

- `audit this site for AI search visibility: https://example.com`
- `GEO audit for our docs subdomain`
- `is my site AI-ready?`
- `LLM citation optimization check on https://example.com`
- `agent readability check on our open-source repo`

The skill clarifies mode (audit / discussion / both), scope (site / repo / both) and depth (full scan / quick) before starting if these are not specified in the request.

## Repository layout

```
claude-agent-readiness-skill/
├── SKILL.md                              # Skill manifest, trigger phrases, workflow
├── knowledge-base.md                     # Evidence base with all tier/confidence tags
├── assets/
│   ├── audit-checks.md                   # Deterministic checks (~50 codes in clusters A–K)
│   ├── qualitative-rubric.md             # 1–5 rubric for content evaluation
│   ├── recommendations-library.md        # Actions sorted by evidence × effort
│   ├── report-template.md                # Output format for the audit report
│   └── discussion-prompts.md             # Sparring prompts on four trade-off axes
├── LICENSE                               # MIT
├── CHANGELOG.md
└── README.md                             # this file
```

## Tier and confidence system

Every claim and recommendation inside the skill is tagged. The system is documented in detail in `SKILL.md` and `knowledge-base.md`. Short version:

| Tag | Meaning |
|---|---|
| **Tier S** | Platform primary source (Google, OpenAI, Anthropic, Bing, Cloudflare, Vercel, published spec sites). |
| **Tier A** | Peer-reviewed or methodologically sound academic research. |
| **Tier B** | Practitioner empirical studies with their own dataset and transparent method. |
| 🟢🟢 | Multi-tested, method-sound (e.g., DiD with matched controls), finding contradicts vendor interest. |
| 🟢 | Solid finding, transparent methodology. |
| 🟡 | Plausible but methodologically limited or n=1. |
| 🔴 | Marketing claim, secondary synthesis without clear primary source, or anecdote. |

Reports must always carry these tags inline with every concrete recommendation; output without tags is not allowed by the skill.

## Limitations

- Knowledge base is dated **2026-05-16**. Platform algorithms drift quarterly — recommendations have a finite half-life. See `knowledge-base.md` section 11 for open questions.
- Most underlying studies are English-language and US/global in scope. Language- and region-specific AI visibility patterns may differ.
- No active crawler telemetry integration (Bing Webmaster Tools, Cloudflare AI Crawl Control). The skill recommends checking those manually.
- The skill does not perform code quality analysis on repositories — only AGENTS.md coverage and structural readability for coding agents.

## Maintenance

Findings in this field age fast. We recommend:

- Reviewing the `knowledge-base.md` quarterly against new platform documentation and major empirical studies.
- Re-checking the headline numbers in tier B (especially Ahrefs follow-ups, which have already corrected the 76% → 38% top-10 overlap once).
- Updating `assets/recommendations-library.md` when new evidence shifts the effort × impact balance.

## Contributing

Contributions are welcome under the MIT license. Useful contributions include:

- New empirical studies with their own dataset that change one of the recommendations.
- Verification of existing tier B numbers against the primary sources.
- Translations (the skill is currently English-only).
- Bug reports for false claims or incorrect tier/confidence assignments.

Please open an issue before submitting a large PR so the change can be discussed first.

## License

MIT — see [LICENSE](./LICENSE).

## Author

Maintained by [byte5 GmbH](https://www.byte5.de) — Christian Wendler, `cwendler@byte5.de`.
