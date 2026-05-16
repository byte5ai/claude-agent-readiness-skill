# Changelog

All notable changes to this skill will be documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and the project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] — 2026-05-16

### Initial release

- `SKILL.md` with trigger phrases, audit workflow, output rules, and tone constraints.
- `knowledge-base.md` covering the mental model of how LLMs find and cite sources, the crawler landscape (OpenAI, Anthropic, Google, Bing, Cloudflare), the fragmented spec ecosystem (llms.txt, AGENTS.md, agent.json, agents.json, ai-agent.json, PAM, schema.org), evidence for what works, hype counters for what does not, and open research questions.
- `assets/audit-checks.md` with ~50 deterministic checks organized in clusters A–K (HTTP, robots.txt, spec files, HTML meta, HTML semantics, JSON-LD, Markdown mirror, OpenAPI, crawler telemetry, code repo, experimental specs).
- `assets/qualitative-rubric.md` with a 1–5 rubric across nine content aspects (fact density, answer-first structure, statistics, expert quotes, linguistic clarity, E-E-A-T signals, answer-block size, cross-format consistency, YouTube/video representation).
- `assets/recommendations-library.md` with 17 evidence-based recommendations (R-01 to R-17) and 6 hype counters (Anti-R-01 to Anti-R-06), each carrying a pre-written audit phrasing for direct report inclusion.
- `assets/report-template.md` with mandatory output format including a "What to skip (hype counter)" section.
- `assets/discussion-prompts.md` with sparring prompts on four trade-off axes (visibility vs. control, effort vs. impact, platform diversification vs. focus, code-repo target audience).
- Tier and confidence system enforced across all recommendations.
- MIT license.
