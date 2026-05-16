---
name: ai-agent-readiness
version: "1.0.0"
author: "byte5 GmbH <cwendler@byte5.de>"
license: MIT
homepage: https://github.com/byte5ai/claude-agent-readiness-skill
description: |
  Analyzes websites and code repos for how well they are discoverable,
  fetchable, and citable by AI search systems (ChatGPT, Claude, Perplexity,
  Google AI Mode, Bing Copilot) and coding agents. Produces a structured
  audit report with score, findings, and prioritized actions, and optionally
  runs a sparring discussion about trade-offs. Trigger phrases:
  "audit this site for AI search visibility", "AI search visibility audit",
  "GEO audit", "LLM optimization check", "agent readability check",
  "AEO audit", "is my site AI-ready?", "is my site AI-optimized?",
  "check our agent readability", "how citable is my content?",
  "LLM citation optimization", "audit website for AI agents",
  "analyze [URL] for AI optimization".
---

# AI Agent Readiness — Website & Repo Audit

## What this skill does — and what it does not

This skill evaluates how well a website or code repo is discoverable, fetchable, parsable, and citable by AI systems — both search LLMs (ChatGPT, Claude, Perplexity, Google AI Mode, Bing Copilot) and coding agents (Codex, Cursor, Aider, Junie, etc.).

It produces two outputs side by side:

1. **An audit report** with deterministic technical checks and a qualitative content assessment. Output includes a score, findings, and prioritized actions.
2. **A sparring discussion** on the trade-offs behind the recommendations — effort vs. impact, visibility vs. control, platform diversification vs. focus.

**What this skill is not:** An SEO audit tool. Classical SEO is only touched where it has been shown to influence AI citations as well (e.g., SEO top-10 as a necessary but not sufficient condition for Google AIO).

**What this skill deliberately does differently from most GEO tools on the market:** It separates hype from evidence. Every recommendation carries a **tier tag** (S/A/B) and a **confidence flag** (🟢🟢/🟢/🟡/🔴). Recommendations with weak evidence are marked as such, not hidden.

## Tier and confidence system (mandatory for every recommendation)

**Tier tags** (origin of the source):
- **S** — Platform primary sources (Google, OpenAI, Anthropic, Bing, Cloudflare, Vercel) and published spec sites. Fact about the platform itself.
- **A** — Peer-reviewed academic research (Princeton GEO/KDD 2024, Toronto 2025, MIT/Columbia E-GEO, EMNLP 2025).
- **B** — Empirical practitioner studies (Ahrefs series, searchVIU, Otterly, individual practitioners).

**Confidence flags**:
- 🟢🟢 — Tested multiple times, methodologically clean (e.g., DiD with matched controls), finding runs against vendor interest. Strongest category.
- 🟢 — Solid finding, transparent methodology, source has no obvious bias toward the result.
- 🟡 — Plausible but methodological weaknesses, n=1, or unclear data provenance.
- 🔴 — Marketing claim, secondary synthesis without a clear primary source, or anecdote.

**Rule:** Every concrete recommendation in the audit or report carries at least one tier tag and one confidence flag — either inline `(Tier B 🟢🟢, Ahrefs DiD)` or in the findings table. Recommendations without an evidence tag are not allowed in the output.

## Workflow

### Phase 0 — Clarify the mode

If the user triggers the skill without a clear directive, ask **once, briefly**:

1. Audit mode: Should a full audit report be produced, or should we first spar about the situation?
2. Scope: Website URL, code repo, or both?
3. Depth: Full scan (all checks from `assets/audit-checks.md`) or quick audit (critical checks only)?

If the user already specified these in the original request, skip Phase 0.

### Phase 1 — Collect data

For websites:
- WebFetch the main page (HTML, HTTP headers).
- WebFetch `/robots.txt`, `/sitemap.xml`, `/llms.txt`, `/AGENTS.md`, `/.well-known/agents.md`, and other Vercel spec paths (see `assets/audit-checks.md`).
- Optionally use Bash for `curl -I` calls when header-specific checks (`Content-Type`, `Vary`, `Link rel="canonical"`) need closer inspection.
- Sample at least 2–3 content subpages (landing, an article/docs page, an API page if applicable).

For code repos:
- WebFetch the GitHub/GitLab repo root.
- Check for the presence of `AGENTS.md`, `CLAUDE.md`, `README.md`, `.cursorrules`, `.cursor/rules`, `docs/`.
- For monorepos: check subproject AGENTS.md coverage.

On any fetch error: note it explicitly, do not guess. If JS rendering is suspected (empty body), that itself is a finding ("JS dependency").

### Phase 2 — Deterministic checks

Work through the check list in `assets/audit-checks.md`. Each check has:
- A unique check code (e.g., `AC-001`)
- A method (what to check, how it is measured)
- Pass/fail/partial criteria
- Tier + confidence of the underlying recommendation
- Default weight (Critical / High / Medium / Low / Bonus)

Result collection: for every check record Pass/Fail/Partial/Skip plus a one-sentence evidence field.

### Phase 3 — Qualitative content assessment

Apply `assets/qualitative-rubric.md` to the sampled subpages. Evaluate:
- Fact density
- Answer-first structure
- Statistic density
- Expert-quote frequency and attribution
- Linguistic clarity
- E-E-A-T signals (author bios, credentials, sourcing)
- Structured answer blocks
- Cross-format consistency (text/image/video)

For each aspect: 1–5 scale plus a 1–2 sentence rationale, with concrete quotes from the content where possible.

### Phase 4 — Consolidate findings

Combine deterministic checks and qualitative assessment into a single findings list. Each finding has:
- Severity (Critical / High / Medium / Low / Bonus)
- Finding (what was observed)
- Source tag (tier + confidence)
- Recommended action (link to a `assets/recommendations-library.md` entry)
- Effort estimate (S/M/L/XL)

**Apply the hype counter actively:** If the user implicitly or explicitly assumes standard GEO myths in their request (e.g., "I need schema for AI citations"), correct this either in the findings section or in a dedicated "What you do *not* need to do" section, with source references from `knowledge-base.md` section 6.

### Phase 5 — Render the report

Follow the template in `assets/report-template.md`. Output format:
- Executive summary (3–5 sentences)
- Site score (Vercel formula + qualitative adjustment)
- Findings table sorted by severity
- Action roadmap (quick wins, mid-term, strategic bets)
- "What you do not need to do" (hype counter, always at least 2–3 items)
- Methodology appendix (which sources at which confidence were used)

If audit + discussion was chosen: after the report, ask an open question that leads to the next trade-off axis (see `assets/discussion-prompts.md`).

### Phase 6 (optional) — Sparring discussion

If the user wants to work in discussion mode (instead of or in addition to the audit), use `assets/discussion-prompts.md`. Three main axes:
1. Visibility vs. control (crawler blocking, pay-per-crawl)
2. Effort vs. impact (Pascal-wager vs. evidence-based)
3. Platform diversification vs. focus

In the discussion, **always** carry tier and confidence tags. Push back when the user takes marketing claims as settled.

## Output rules

- **Format**: Tables for structured comparisons, prose for analysis and recommendations. No bullet-list bloat. If a finding needs one sentence, write one sentence.
- **Source transparency**: At the end of the report, a "Methodology & Sources" block listing the knowledge-base sections used.
- **Honesty about uncertainty**: Where the field has no consensus (see `knowledge-base.md` section 11), name it explicitly in the report. "We do not know" is a valid recommendation.
- **Language**: Mirror the user's language (typically German or English).
- **Form of address**: Mirror the user's tone. If unclear from context, write neutrally ("the team should…", "the site should…").
- **Tone**: Professional, factual, direct, no marketing fluff. No ironic asides, no sarcasm, no insider jokes — the output must be equally readable for engineering, marketing, account management, and external stakeholders. Avoid "revolutionary" / "cutting-edge" / "must-have" vocabulary.
- **Phrasing of actions (critical)**: Every recommendation in the audit report is written as a **complete, self-contained sentence or paragraph** — not as an R-XX code with keywords. Insider terms (author schema, Markdown mirror, content negotiation, JSON-LD, etc.) are **briefly explained** on first use in the report (a half sentence is enough). R-XX codes may appear at the end of an action as a reference tag, but never replace the plain-language text. If a reader without prior knowledge cannot derive from the action what to do, the phrasing is not acceptable.

## Important don'ts

- **Do not sell schema markup as an AI citation booster.** Tier B 🟢🟢 disproves this. Recommend schema only when rich results, knowledge graph, or voice search are relevant.
- **Do not frame llms.txt as required for AI visibility.** Google explicitly says it is not needed. Pascal-wager: creating one is trivial and harmless — but sell it that way, not as a lever.
- **Do not recommend AGENTS.md without a drift note.** If created, then with a CI maintenance hook. An out-of-sync AGENTS.md feeds agents wrong information with an authority signal attached.
- **Do not cite marketing phrases** without a primary source. "AI search loves fresh content" is only partly true (Google AIO actually prefers older content).
- **No pseudo-precision.** "47% more citations through tables" without a primary source is either marked 🔴 or omitted.

## Files in this skill

- `SKILL.md` — this file.
- `knowledge-base.md` — the knowledge base with all evidence, tier tags, and confidence flags. **First reference for every recommendation.**
- `assets/audit-checks.md` — deterministic check list (technically verifiable).
- `assets/qualitative-rubric.md` — rating scale for content-level aspects.
- `assets/recommendations-library.md` — action library, sorted by effort × impact.
- `assets/report-template.md` — output format for the audit report.
- `assets/discussion-prompts.md` — sparring questions for trade-off discussions.

## Limitations & roadmap

What this skill does not cover in v1:
- Multi-language / regional optimization (the research base is primarily English).
- E-commerce-specific E-GEO heuristics (a separate sub-skill is conceivable).
- Active crawler telemetry (Bing Webmaster Tools, Cloudflare AI Crawl Control) — could be added later via MCP.
- Repository code-quality analysis (only AGENTS.md coverage, not the code itself).

If the field drifts significantly (platform updates, new specs), `knowledge-base.md` must be updated, otherwise recommendations go stale. The knowledge-base date is noted in the file header.
