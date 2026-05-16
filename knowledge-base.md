# Knowledge Base: AI/Agent Website Optimization

**Last updated:** 2026-05-16

---

## 1. How to read this

**Tier tags** indicate the type of source:

- **Tier S** — Primary sources from the affected platforms themselves (Google, OpenAI, Anthropic, Bing, Cloudflare, Vercel) and published spec sites (llmstxt.org, agents.md). What a platform writes is fact about itself — claims about effectiveness drawn from these sources are still self-reporting.
- **Tier A** — Peer-reviewed or methodologically rigorous academic research (Princeton GEO/KDD 2024, Toronto 2025, MIT/Columbia E-GEO, EMNLP 2025 Findings). Highest causal reliability, but always samples from a system that changes quickly.
- **Tier B** — Empirical practitioner studies (mainly Ahrefs, Otterly, Search Engine Land, searchVIU, individual practitioners). Large datasets, transparent methodology, but mostly tool-vendor bias and typically cross-sectional without control groups.

**Confidence flags** complement the tier:

- 🟢🟢 Tested multiple times, methodologically clean (e.g., DiD with matched controls), finding runs against vendor interest.
- 🟢 Solid finding, transparent methodology.
- 🟡 Plausible, but methodological weaknesses or unclear data provenance.
- 🔴 Marketing claim, secondary synthesis without a clear primary source, or anecdote (n=1).

Where sources disagree, both positions are stated. Where the field has no consensus, that is named explicitly.

---

## 2. Mental model — how LLMs find and cite sources

An LLM answer is not produced by tapping a single source but through a multi-stage pipeline. Synthesis from ZipTie Perplexity Pipeline (Tier B 🟡), Princeton GEO (Tier A 🟢), Google AI Optimization Guide (Tier S 🟢), and Bing AI Performance Preview (Tier S 🟢):

1. **Discovery / Indexing.** Crawlers (GPTBot, ClaudeBot, OAI-SearchBot, Claude-SearchBot, Bingbot, Googlebot, CCBot) read pages continuously. What is not indexable cannot be cited. Google makes this eligibility explicit: "A page must be indexed and eligible to appear with a snippet" (Tier S 🟢).
2. **Query fan-out.** For a user question the system generates multiple sub-queries. According to Ahrefs (Tier B 🟢), Gemini 3 amplifies this significantly compared to Gemini 2 — the corrected top-10 overlap (76% → 38%) is explained precisely by this mechanic.
3. **Real-time web retrieval / RAG grounding.** Hybrid search combining BM25 (lexical) and Dense Embeddings (semantic). Microsoft calls the user-visible output "citations" and the internal retrieval step "grounding queries" (Bing AI Performance, Tier S 🟢). Otterly (Tier B 🟡) shows the ratio for one example site: 30,398 grounding events vs. ~169 visible citations — **99.6% of the AI influence stays invisible**.
4. **Fetch.** During live retrieval the system actually reads the page. searchVIU (Tier B 🟡, cited in the Ahrefs schema study Tier B 🟢🟢 and SEL Tier B 🟡) tested five AI systems (ChatGPT, Claude, Perplexity, Gemini, Google AI Mode): all extract **only visible HTML**; JSON-LD and hidden Microdata are ignored.
5. **Multi-layer ML ranking / reranker.** Cascading filters: semantic relevance → freshness → structural quality → authority → engagement (ZipTie, Tier B 🟡). Single-signal optimization fails at any stage where another signal is missing.
6. **Structured prompt assembly + LLM synthesis.** Pre-embedded citations are passed to the model along with the retrieved evidence, and the model generates the answer.
7. **Citation rendering.** What appears in the UI as a citation is only a fraction of the sources actually consulted.

**Consequence for the audit:** Every step filters. A page can fail at step 1 (robots.txt blocks OAI-SearchBot) or at step 4 (critical information sits only inside a JSON-LD block) — the effect on the end user is the same (no citation), but the root cause is fundamentally different.

---

## 3. Crawler landscape (technical)

| Platform | Bot | Purpose | robots.txt token | User-agent string (excerpt) | IP list |
|---|---|---|---|---|---|
| OpenAI | OAI-SearchBot | ChatGPT search results | `OAI-SearchBot` | `…compatible; OAI-SearchBot/1.3; +https://openai.com/searchbot` | openai.com/searchbot.json |
| OpenAI | GPTBot | Training foundation models | `GPTBot` | `…compatible; GPTBot/1.3; +https://openai.com/gptbot` | openai.com/gptbot.json |
| OpenAI | ChatGPT-User | User-initiated fetches | `ChatGPT-User` | `…compatible; ChatGPT-User/1.0; +https://openai.com/bot` | openai.com/chatgpt-user.json |
| Anthropic | ClaudeBot | Training | `ClaudeBot` | (not fully published in the article) | claude.com/crawling/bots.json |
| Anthropic | Claude-User | User-initiated fetches | `Claude-User` | same | same |
| Anthropic | Claude-SearchBot | Search index / quality | `Claude-SearchBot` | same | same |
| Google | Googlebot + Google-Extended | Search + AI Overviews / AI Mode | `Googlebot`, `Google-Extended` | published in Google crawler docs | — |
| Microsoft | Bingbot | Search + Copilot | `Bingbot` | published | — |
| Common Crawl | CCBot | Public crawl corpus, used by many LLMs as a training source | `CCBot` | published | — |

**Mechanics of the separation** (all Tier S 🟢): OpenAI and Anthropic explicitly split training, search, and user-driven fetching into three separate bots. A site owner can allow `OAI-SearchBot` (preserving ChatGPT search visibility) and disallow `GPTBot` (no training on the site's content). Accidentally blocking `OAI-SearchBot` removes ChatGPT search visibility — a common pitfall, because many "AI block" snippets in forums lump everything together.

**ChatGPT-User and Claude-User** are user-initiated: in practice `robots.txt` does not reliably govern these because they are not bulk crawls. Anthropic openly acknowledges this (Tier S 🟢); OpenAI documents the bots but does not comment on `ChatGPT-User`'s robots.txt conformance with the same clarity.

**Example robots.txt directives** (Tier S 🟢, from Anthropic Help):

```
User-agent: ClaudeBot
Disallow: /

User-agent: ClaudeBot
Crawl-delay: 1
```

Anthropic honors the non-standard `Crawl-delay` directive. Google ignores it. To block via robots.txt with Anthropic, each subdomain must be handled separately.

**Verification by IP** is possible with OpenAI, Anthropic, and Google (official bot IP lists as JSON) but is not relevant for static site audits. Cloudflare AI Crawl Control (Tier S 🟢) tracks these IPs at the platform level and allows allow/block/charge per crawler.

**Cloudflare Pay-per-Crawl** (Tier S 🟢, private beta since July 2025): HTTP 402 + `crawler-price: USD XX.XX` header, coupled with Web Bot Auth (RFC 9421, Ed25519). Low relevance for a standard audit; only relevant if the site owner is considering monetization instead of blocking.

---

## 4. Standards & specs — a fragmented field

| Spec | Path | Audience | Adoption | Status |
|---|---|---|---|---|
| **llms.txt** | `/llms.txt` (root) | Content discovery for LLMs | Docs sites (FastHTML, nbdev), tooling exists; no LLM vendor has publicly committed | Proposal Sep 2024 (Jeremy Howard, Answer.AI), no standardization |
| **AGENTS.md** | Repo root | Coding agents in the repo | ~60k OSS repos; >20 tools (Codex, Cursor, Aider, Junie, Copilot, Windsurf, Devin, etc.); Linux Foundation-stewarded | De facto convention, "no required fields" |
| **Vercel Agent Readability** | Consolidation of multiple paths | Site-wide audit framework | Only Vercel itself | Vercel's own curation, not an open standard; very detailed |
| **AWP agent.json** | `/agent.json` or `/.well-known/agent.json` | Action manifest for sites | 1 live deployment (laclawclaw.com); no major vendor | Draft RFC v0.1 March 2026, v0.2 in progress |
| **Wildcard agents.json** | `/.well-known/agents.json` | OpenAPI-based action manifest | ~6 example integrations, own registry | v0.1.0 Feb 2025, repo inactive since |
| **Aiia ai-agent.json** | unknown | unknown | only the owner | JS-SPA spec, content not accessible without a browser — strong negative signal |
| **JSON Agents PAM** | (describes agents, not sites) | Agent manifest, framework-agnostic | none known | Clean schema, wrong category for website audits |
| **schema.org JSON-LD** | inline per page | Structured data for all crawlers | Industry standard since 2011 | Active, **the only spec with confirmed inference-time use** |

**Honest take on who actually reads what** (Tier S synthesis, May 2026):

- **Schema.org JSON-LD** is the only spec with demonstrable, broad consumption by Google, Bing, and indirectly through them by trained LLMs. **However:** during live retrieval by ChatGPT, Claude, Perplexity, Gemini, and AI Mode, JSON-LD is ignored (searchVIU, Tier B 🟡; see section 6.1). Schema therefore works via training / knowledge graph, not via fetch.
- **robots.txt + sitemap.xml** are respected by all major AI crawlers (Tier S 🟢, confirmed multiple times).
- **AGENTS.md** is read by >20 coding agents in code repos — but that is repo context, not web crawling.
- **llms.txt** has tooling and directories, but **no major LLM provider has publicly confirmed that it reads it at inference time**. Google even says explicitly that it is not needed (see section 6.2). Vercel and Cloudflare recommend it without providing impact evidence.
- **agent.json (AWP), agents.json (Wildcard), ai-agent.json (Aiia), PAM** are marketing or early-stage projects; label them "experimental" for a standard audit.

**Conflict between specs:** Vercel accepts eight different paths for its "skill file" requirement (`/AGENTS.md`, `/agents.md`, `/.well-known/agents.md`, `/docs/AGENTS.md`, `/llms-full.txt`, `/CLAUDE.md`, `/.cursor/rules`, `/.cursorrules`) — a pragmatic consolidation of competing conventions that the original specs do not anticipate. agent.json (singular, AWP) and agents.json (plural, Wildcard) target the same slot but have different schemas and file names.

---

## 5. What demonstrably works (with confidence)

### 5.1 Content structure

**Answer-first pattern (H2 with a direct answer), short paragraphs, clear H2/H3 hierarchy.** Lawrence Hitches (Tier B 🟡, n=1) observes this anecdotally for Copilot; consistent with the ZipTie pipeline logic (Tier B 🟡), which names "structural quality" as its own filter stage. Princeton GEO (Tier A 🟢) tests fluency and easy-to-understand language as separate tactics — both with a moderate positive effect. Vercel (Tier S 🟢) requires at least three section headings per page and a text-to-HTML ratio > 15% as an operationalized minimum.

**No hard effect sizes** beyond Princeton's "moderate increase" — the reliable claim is the direction, not the lift.

### 5.2 Content signals (statistics, quotes, citations)

Princeton GEO (Tier A 🟢, KDD 2024, n=>10,000 queries): Statistics Addition and Quotation Addition are among the strongest tactics tested, with reported lifts up to roughly **+30–40%** on the Position-Adjusted Word Count Visibility metric. Citing Sources also shows a noticeable increase. **Important: the verifiable ceiling in the abstract is +40% combined** on the authors' own engine prototype; the individual values per tactic come from the paper's table and could not be verified from the full text in the research process.

Consistent with the earned-media-bias finding (Toronto 2025, Tier A 🟢): LLMs systematically prefer "verifiable, attributable, third-party-corroborated" content.

### 5.3 Freshness

Ahrefs 17M citation study (Tier B 🟢): AI assistants cite content that is on average **25.7% younger than organic SERP results** (1,064 vs. 1,432 days since publication). Ranking by freshness preference:

| Surface | Days since publication |
|---|---|
| ChatGPT (citations) | 958 |
| ChatGPT (references) | 1,023 |
| Copilot | 1,056 |
| Gemini | 1,118 |
| Perplexity | 1,166 |
| Google AIO | 1,432 |
| Organic SERP | 1,416 |

For "days since last updated" the spread shrinks to 13.1%. **Important distinction:** Google AIO shows **no** freshness preference — slightly the opposite of organic SERP. For AIO, freshness is not a lever.

### 5.4 Authority / E-E-A-T / entity recognition

**Treat with caution.** Digital Bloom (Tier B 🟡→🔴 for individual numbers) synthesizes several third-party studies: brand search volume as the strongest predictor with a quoted correlation of 0.334 from a "7,000 citations / 1,600 URLs" analysis — the primary source is not clearly traceable in the report. Multi-platform presence (4+) correlates with a 2.8× higher probability of appearing in ChatGPT responses. **Direction is consistent** across several reports (brand signals > backlinks), magnitude disputed.

Bing AI Performance (Tier S 🟢) recommends "strengthen depth and expertise" and consistency across text/images/video for the same entities — generic quality signals, no quantified effects. SemRank (Tier A 🟢, EMNLP 2025 Findings) provides the theoretical argument for conceptual indexing (glossary, topic hubs, entity markup): concept-based indexing + LLM query understanding with corpus grounding consistently beats naive dense retrieval.

### 5.5 Classical SEO as a baseline — with a correction

The Ahrefs headline "**76% of AIO citations come from the top 10**" (Tier B 🟡, n=1.9M citations July 2025) was corrected by the Ahrefs follow-up study in March 2026 (Tier B 🟢, n=4M AIO URLs) to **37.9%**. For blue links alone: 37.1% top-10, 26.2% pos 11–100, **36.7% not in the top-100**. Co-author SQ Ong: "If you rank #1 in the SERPs … that chance is a coin flip at best."

And for the other AI assistants (Ahrefs 15K long-tail study, Tier B 🟢): only **12% average overlap** between AI citations and Google top-10 for the same query. Perplexity 28.6% (highest), Gemini 8.0%, Copilot 6.1%, ChatGPT 8.6%. **80% of AI citations** do not rank in the top-100 for the original query.

**Consequence:** SEO top-10 remains a **necessary but not sufficient** condition for AIO. For ChatGPT/Perplexity/Gemini it is not even necessary in the classical sense — the query fan-out mechanic pulls sub-query SERPs.

### 5.6 Brand signals / YouTube

Ahrefs March 2026 (Tier B 🟢): **18.2%** of non-ranking AIO citations are **YouTube URLs**; YouTube accounts for 5.6% of all AIO citations and has become the most-cited domain in AIOs, up 34% in 6 months. Bing Copilot Optimization (Hitches, Tier B 🟡, n=1) confirms: Bing openly uses social signals (LinkedIn, X, Reddit), in contrast to Google.

### 5.7 Markdown mirrors & content negotiation

Vercel (Tier S 🟢) demands this in detail: every HTML page has a `.md`/`.mdx` mirror with frontmatter (`title`, `description`, `doc_version`, `last_updated`); the HTML carries `<link rel="alternate" type="text/markdown">`; the server responds to `Accept: text/markdown` with `Content-Type: text/markdown; charset=utf-8` and `Vary: Accept`. Cloudflare (Tier S 🟢) implements this itself and uses an HTML header to explicitly invite LLMs to use the `index.md` variant.

**Impact evidence is missing.** Vercel provides no A/B data showing that Markdown mirrors raise the citation rate. This is a plausible best practice recommended by two platforms, without empirical validation outside those platforms themselves.

### 5.8 AGENTS.md for code repos

Tier S 🟢 (agents.md, Linux Foundation-stewarded): "used by over 60k open-source projects". Recommended sections: project overview, build/test commands, code style, testing instructions, security considerations, PR instructions. For monorepos, a separate AGENTS.md per subproject (OpenAI's own main repo: 88 AGENTS.md files). Conflict resolution: the closest file wins, explicit user prompts override everything.

Solid as a repo standard, **not** as a website asset. Vercel's extension (AGENTS.md also in the web root) is Vercel's curation, not part of the official spec.

---

## 6. What does NOT (or barely) work — hype counter

This section is given the same weight as the previous one. Negative findings are especially valuable in a field dominated by marketing.

### 6.1 Schema.org / JSON-LD during live AI fetch

**Hype:** JSON-LD is pitched as an AI visibility booster in LinkedIn carousels, GEO conferences, and agency listicles. Often quoted: "53% of AI-cited pages have schema."

**Data (Ahrefs schema study, Tier B 🟢🟢 — highest confidence of the entire research base):** Quasi-experimental design with matched controls + difference-in-differences, n=1,885 treated + 4,000 controls, 30-day window before and after treatment, four different statistical tests.

| Surface | Effect after JSON-LD addition |
|---|---|
| AIO | **−4.6%** (significant but small) |
| AI Mode | +2.4% (not distinguishable from zero) |
| ChatGPT | +2.2% (not distinguishable from zero) |

The 53% correlation from the cross-section before matching is a spurious correlation: pages with schema are technically better-maintained pages that get cited for other reasons.

**searchVIU experiment** (Tier B 🟡, referenced in the Ahrefs study and SEL Tier B 🟡): ChatGPT, Claude, Perplexity, Gemini, and Google AI Mode all extract **only visible HTML** during direct retrieval. JSON-LD, hidden Microdata, and hidden RDFa are all ignored.

**Caveat (named by the author himself):** the study tested pages that already had ≥100 AIO citations. For pages that have never been cited, schema might still help during initial discovery — neither disproven nor proven.

**Recommendation:** Schema remains useful for other reasons (rich results, voice, knowledge graph, Google's entity understanding via training). **Do not** pitch it as a direct AI citation lever.

### 6.2 llms.txt — adoption disputed

Vercel and Cloudflare recommend `llms.txt` (Tier S 🟢, both). **Google says explicitly in its official AI Optimization Guide that `llms.txt` and content chunking are not necessary** (Tier S 🟢). That is a direct contradiction between platform sources.

The llmstxt.org spec itself (Tier S 🟢) says "we expect" and "perhaps if widespread" — hope, not adoption evidence. **No** major LLM vendor (OpenAI, Anthropic, Google) has publicly committed to reading llms.txt at inference time. HN threads and discussions on llms-txt.io debate exactly this gap; the "60k+ projects" claims from other specs are GitHub code-search counts, not proof of consumption.

**Pragmatics:** Creating a `/llms.txt` file is trivial and harmless. If the customer uses Vercel or Cloudflare, it is consistent. Selling it as "required for AI visibility" is not supported.

### 6.3 Keyword stuffing as a negative effect

Princeton GEO (Tier A 🟢): keyword stuffing shows **no positive effect, sometimes slightly negative**, in the GEO setting — unlike in classical SEO. A robustly replicated negative finding.

### 6.4 Marketing claims that do not hold up in the data

**"47% higher citation rate for comparison tables with proper HTML"** — frequently cited as settled in GEO listicles. Primary source at Digital Bloom (Tier B 🟡→🔴) not clearly traceable.

**"AI search loves fresh content — update everything often"** — data: the average age of cited content is **2.9 years** (Ahrefs 17M, Tier B 🟢). Google AIO even slightly prefers *older* content. John Mueller explicitly warns against pure date-bumping; Lawrence Hitches (Tier B 🟡) reports anecdotally that Bing flags it as a "deception flag".

**"Programmatic SEO scales in AI search"** — Otterly (Tier B 🟡, n=1): programmatic pages account for only 4.8% of Copilot citations; **5 pages generate 74.6% of all citations**. Power law dominates, not volume. n=1 — not proof, but consistent with other concentration findings.

**"Backlinks remain the central factor"** — multiple secondary syntheses (Digital Bloom, Tier B 🟡) report weak to neutral correlation between backlinks and LLM citations. Brand search volume is a stronger predictor. Direction consistent, magnitude disputed.

**"AI search is a completely new game"** — in practice, most "new" levers (entity coverage, topic clusters, long-tail content) are extensions of classical content strategy. For AIO, SEO top-10 is still a necessary precondition; for ChatGPT/Perplexity, earned media + topic coverage matter more than purely SEO-oriented tweaks (Toronto 2025, Tier A 🟢).

---

## 7. Technically verifiable checks (for audit mode)

Deterministic checks that can be executed via WebFetch or shell. The backbone is the Vercel Agent Readability Spec (Tier S 🟢), supplemented by crawler-token checks from the platform docs.

**HTTP / performance**

- HTTP status 200, max. 1 redirect (2+ = fail).
- HTTPS enforced.
- Page speed (mobile <3s is Bing's expectation per Hitches, Tier B 🟡).
- `Content-Type` correct: `text/html;charset=UTF-8` for HTML, `text/plain;charset=UTF-8` for Markdown.
- `x-robots-tag` must not contain `noindex`, `noai`, or `noimageai`.

**robots.txt directives (per AI bot, all Tier S 🟢)**

- Existence of `/robots.txt`.
- No blocking of `GPTBot`, `OAI-SearchBot`, `ChatGPT-User`, `ClaudeBot`, `Claude-User`, `Claude-SearchBot`, `Googlebot`, `Google-Extended`, `Bingbot`, `CCBot`.
- Subdomain awareness (Anthropic requires a separate robots.txt per subdomain).
- `Crawl-delay` if set is officially respected only by Anthropic.
- `/llms.txt` not disallowed.

**Spec files**

- `/llms.txt` at the root, `/.well-known/llms.txt`, or `/docs/llms.txt`. Content-Type `text/plain`, not empty, at least one H1, at least one H2 with a Markdown link list. URLs should end in `.md`/`.mdx`, not `.html`.
- `/AGENTS.md` (or Vercel alternatives: `/agents.md`, `/.well-known/agents.md`, `/docs/AGENTS.md`, `/llms-full.txt`, `/CLAUDE.md`, `/.cursor/rules`, `/.cursorrules`). At least 2 of Installation/Configuration/Usage. High repo relevance, low site relevance (Vercel's interpretation).
- `/sitemap.xml` with `<lastmod>`, valid `<urlset>` or `<sitemapindex>`.
- `/sitemap.md` (Vercel).

**HTML meta**

- `<link rel="canonical">` required.
- `<meta name="description">` ≥50 characters.
- `<meta property="og:title">`, `<meta property="og:description">`.
- `lang` attribute on `<html>`.
- `hreflang` on multi-language sites.

**HTML semantics**

- At least 3 section headings (h1–h3) per page.
- Text-to-HTML ratio > 15%.
- Lists and tables present where appropriate.

**JSON-LD presence and validity**

- `<script type="application/ld+json">` containing at minimum: title, description, canonical URL, `dateModified`, `BreadcrumbList`.
- Type matched to the page (Article/TechArticle, FAQPage, HowTo, Product, Organization, Person).
- **Important for the output:** Check validity, but communicate the effect on AI citations soberly (section 6.1).

**JS rendering dependency**

- Essential content readable without JS execution (SPA delivery makes AI fetch harder — see the OpenAI `/docs/gptbot` example, Tier S 🟢, which itself returns an empty body).

**Markdown mirror availability (Tier S 🟢 Vercel + Cloudflare)**

- `.md` suffix works per URL.
- `<link rel="alternate" type="text/markdown">` in the HTML.
- Server responds to `Accept: text/markdown` with `Content-Type: text/markdown; charset=utf-8` + `Vary: Accept`.
- Markdown frontmatter (`title`, `description`, `doc_version`, `last_updated`).
- `Link: <canonical-url>; rel="canonical"` header in the Markdown response.

**OpenAPI schemas**

- On API docs pages (URLs with `/api/`, `/reference/`, `/endpoints/`, `/swagger/`, `/openapi/`) links to `openapi.json`/`swagger.json`/`swagger.yaml`/`schema.json`.
- All `<pre><code>` with `language-*` or `lang-*` class.

**Vercel score formula**

`score = round((passed / total) × 100)`; 90+ Excellent, 70–89 Good, 50–69 Fair, 0–49 Needs Improvement. 15 site-wide checks + 23 per-page checks. **Per-page checks compound**: more pages = larger denominator; systemic issues amplify, individual page failures dilute.

---

## 8. Aspects to evaluate qualitatively (LLM judgment part)

These aspects cannot be checked binarily; they require semantic evaluation of the page content. Evidence from Tier A and Tier S:

- **Fact density.** Verifiable claims per paragraph. Princeton GEO (Tier A 🟢): Statistics Addition is a top-3 lever.
- **Answer-first structure.** H2 with a direct answer, followed by rationale. Consistent with Hitches Tier B 🟡 + ZipTie Tier B 🟡.
- **Statistic density.** Concrete numbers, ideally with date and source.
- **Expert-quote frequency and attribution.** Princeton GEO: Quotation Addition is also a top lever. Attributed quotes without fabricated sources.
- **Linguistic clarity.** Princeton: fluency optimization and easy-to-understand language show moderate effects.
- **E-E-A-T signals.** Author bios with verifiable credentials, sources, dateModified, Organization schema, Author schema. Bing AI Performance (Tier S 🟢) recommends this explicitly; supported by correlation in Tier-B syntheses (Tier B 🟡), not isolated causally in any study.
- **Structured answer blocks (50–150 words).** Plausible from the RAG chunking mechanic (each chunk is a potential answer unit), not empirically quantified.
- **Consistency across text/image/video** (Bing AI Performance, Tier S 🟢): same entities, products, and concepts across media formats.

---

## 9. Code-repo specific

For repos (as opposed to marketing/content sites):

- **AGENTS.md in the repo root** (Tier S 🟢). Recommended sections: project overview, build/test commands (with exact shell commands), code style, testing instructions, security considerations, PR instructions. For monorepos, one per subproject; nearest file wins.
- **/docs as a Markdown mirror** of the HTML documentation. Every docs page with a `.md` variant.
- **OpenAPI/Swagger** at standardized paths (`/openapi.json`, `/swagger.json`). Linked from API docs pages.
- **Examples in code fences with language tags** (`language-python`, `lang-bash`, etc., Vercel Tier S 🟢).
- **Example curl calls** for every endpoint (operational best practice, implicit in the Vercel spec).
- **CLAUDE.md / `.cursorrules` / `.cursor/rules`** as compatible coding-agent files. The agents.md site recommends symlinks for migration.

Wildcard agents.json (`/.well-known/agents.json`) and AWP agent.json (`/agent.json`) target API action manifests but have practically no adoption — mark as "bonus" or "experimental".

---

## 10. Discussion framework (for sparring mode)

Three main axes along which trade-offs can be discussed:

**Visibility vs. control.** Blocking GPTBot/ClaudeBot entirely costs no search visibility (that runs through OAI-SearchBot and Claude-SearchBot — Tier S 🟢), but also no training influence. Cloudflare Pay-per-Crawl (Tier S 🟢, private beta) offers a middle position: allow / charge / block per crawler. Question to the site owner: What is the business model? Ad traffic? Brand building? Content as an asset? — the answer determines whether training bots should be allowed or blocked.

**Effort vs. impact (Pascal-wager vs. evidence-based).** Creating an llms.txt takes 10 minutes, the impact is not proven but harmless → the Pascal-wager logic justifies it. Full Markdown mirrors with content negotiation take engineering weeks, with no proven impact → harder to justify without further empirical evidence. Schema markup is medium effort with practically zero effect on AI citations (Tier B 🟢🟢) — but rich results and knowledge graph justify it indirectly. The skill should explicitly contrast effort and proven impact.

**Platform diversification vs. focus.** AI search has heterogeneous sourcing heuristics (Toronto 2025, Tier A 🟢; Ahrefs 15K study, Tier B 🟢). ChatGPT prefers fresher content, Google AIO older. Perplexity has high domain diversity, Copilot more concentrated citations. A universal strategy is methodologically dubious. Question: Which AI surface is commercially relevant for the customer? — prioritize accordingly, do not optimize everything at once.

---

## 11. Open research questions / what we do not know

Listed honestly, because platform algorithms are opaque and the GEO literature is young.

- **Inference-time consumption of llms.txt** by major LLM vendors is not publicly confirmed. Tooling and directories exist; proof of consumption does not.
- **Effect of Markdown mirrors** on citation rate is recommended by Vercel and Cloudflare but has not been measured in an independent study.
- **Schema effect during initial discovery** (pages that have never been cited) is not disproven; the Ahrefs study included pages with ≥100 AIO citations.
- **Platform drift.** The Ahrefs top-10 study shifted from 76% to 38% within 8 months — findings are often months old, and the LLM substrate changes quarterly.
- **Language and regional differences.** All studies found are primarily English. German/European AI visibility patterns may look different.
- **Causality for earned-media bias.** Toronto 2025 (Tier A 🟢) shows the bias robustly but says nothing about the *how* — how does one produce earned-media mentions?
- **Backlinks vs. brand search volume.** Digital Bloom (Tier B 🟡→🔴) quotes a correlation of 0.334 without a clear primary source. Direction consistent, magnitude unconfirmed.
- **AGENTS.md as a website asset** (Vercel's extension): proven in repos, not proven in the web root.
- **agent.json / agents.json / ai-agent.json / PAM** — all early stage, no adoption by major vendors, impact evidence = zero.
- **Princeton GEO generalizability.** The authors' own engine prototype; transferability to ChatGPT/Perplexity/Gemini is an assumption, not a result.
- **E-GEO universal pattern.** "Universally effective" is claimed, but the risk of overfitting to the optimizing LLM is not ruled out.
- **Source-Coverage paper (arXiv 2512.09483).** Full text not verifiable from the research process; content is null until manually checked.

---

## 12. Source register

| Source | Tier | Confidence | URL | Core weight |
|---|---|---|---|---|
| Google AI Optimization Guide | S | 🟢 | developers.google.com/search/docs/fundamentals/ai-optimization-guide | Platform position on AIO/AI Mode, llms.txt not needed |
| OpenAI Crawlers Overview | S | 🟢 | developers.openai.com/api/docs/bots | Three OpenAI bots, UA strings, IP lists |
| OpenAI GPTBot Docs | S | 🟢 | platform.openai.com/docs/gptbot | Not reachable via WebFetch (SPA) — content covered by Crawlers Overview |
| Anthropic Crawler Help | S | 🟢 | support.claude.com/en/articles/8896518 | Three Anthropic bots, robots.txt examples, Crawl-delay |
| Vercel Agent Readability Spec | S | 🟢 | vercel.com/kb/guide/agent-readability-spec | Backbone of the audit framework, 38 checks |
| Vercel Docs-readable-by-Agents | S | 🟢 | vercel.com/kb/guide/make-your-documentation-readable-by-ai-agents | Discovery/retrieval/tool-access model |
| Cloudflare AI Crawl Control | S | 🟢 | developers.cloudflare.com/ai-crawl-control/ | Platform tool, llms.txt distribution, Markdown recommendation |
| Cloudflare Pay per Crawl | S | 🟢 | blog.cloudflare.com/introducing-pay-per-crawl/ | HTTP 402, Web Bot Auth, optional |
| Bing AI Performance Preview | S | 🟢 | blogs.bing.com/webmaster/February-2026/Introducing-AI-Performance | Grounding queries vs. citations, IndexNow, Bing Webmaster Tools |
| llmstxt.org Spec | S | 🟢 | llmstxt.org | Original llms.txt spec (Howard, Sep 2024) |
| agents.md Spec | S | 🟢 | agents.md | Original AGENTS.md spec, LF-stewarded |
| agents.md Repo | S | 🟢 | github.com/agentsmd/agents.md | Marketing site + example |
| Agent Web Protocol | S | 🟡 | agentwebprotocol.org | agent.json singular, draft v0.1, 1 live deployment |
| agent-json.org | S | 🟡 | agent-json.org | Sister site of AWP |
| Wildcard agents.json | S | 🟡 | github.com/wild-card-ai/agents-json | agents.json plural, OpenAPI-based, ~6 examples |
| Aiia ai-agent.json | S | 🟡 | aiia.ro/spec/ai-agent-json | JS-SPA, content not accessible |
| JSON Agents PAM | S | 🟡 | jsonagents.org | Agent description, wrong spec category for sites |
| schema.org | S | 🟢 | schema.org | JSON-LD, only spec with confirmed inference-time use (via training) |
| Princeton GEO | A | 🟢 (abstract) / 🟡 (tactic values) | arxiv.org/abs/2311.09735 | KDD 2024, +40% visibility, Statistics+Quotes top levers, keyword stuffing null |
| Toronto Earned-Media | A | 🟢 | arxiv.org/abs/2509.08919 | Systematic earned-media bias in AI search vs. Google |
| E-GEO (MIT/Columbia) | A | 🟢 | arxiv.org/abs/2511.20867 | E-commerce GEO, optimization framework beats 15 heuristics |
| Source Coverage Paper | A | 🟡 | arxiv.org/html/2512.09483v1 | Full text not verifiable — check manually |
| SemRank (EMNLP 2025) | A | 🟢 | arxiv.org/abs/2505.21815 | Concept-based indexing + LLM query understanding |
| Ahrefs 76% study | B | 🟡 | ahrefs.com/blog/search-rankings-ai-citations/ | n=1.9M citations July 2025, corrected in 2026 |
| Ahrefs 38% update | B | 🟢 | ahrefs.com/blog/ai-overview-citations-top-10/ | n=4M AIO URLs March 2026, YouTube 18.2% of off-rank citations |
| Ahrefs Schema null finding | B | 🟢🟢 | ahrefs.com/blog/schema-ai-citations/ | DiD, n=1,885 + 4,000 controls, no measurable schema effect |
| Ahrefs Freshness 17M | B | 🟢 | ahrefs.com/blog/do-ai-assistants-prefer-to-cite-fresh-content/ | 25.7% younger than SERP, AIO not freshness-preferring |
| Ahrefs AI Content Bias | B | 🟡 | ahrefs.com/blog/ai-overviews-cite-ai-generated-content-more-than-human-writing/ | AI detector proprietary, treat with caution |
| Ahrefs Overlap Study | B | 🟢 | ahrefs.com/blog/ai-search-overlap/ | 12% average overlap AI ↔ Google top-10 for original query |
| Ahrefs Synthesis | B | 🟡 | ahrefs.com/blog/how-to-rank-in-ai-overviews/ | Aggregation, less original empirical work |
| SEL Schema Hype | B | 🟡 | searchengineland.com/schema-markup-ai-search-no-hype-472339 | Secondary source on the schema null finding, references searchVIU |
| searchVIU Schema Test | B | 🟡 | searchviu.com/en/schema-markup-and-ai-in-2025… | 5 AI systems, none reads JSON-LD during live retrieval |
| Otterly Bing AI Report | B | 🟡 | otterly.ai/blog/bing-webmaster-tools-ai-performance-report/ | n=1, grounding/citation asymmetry 99.6% |
| Digital Bloom 2025 | B | 🟡→🔴 (individual numbers) | thedigitalbloom.com/learn/2025-ai-citation-llm-visibility-report/ | Secondary synthesis, brand-search-volume thesis |
| Lawrence Hitches Copilot | B | 🟡 | lawrencehitches.com/copilot-search-optimization/ | n=1 case study, IndexNow, social signals |
| ZipTie Perplexity | B | 🟡 | ziptie.dev/blog/how-perplexity-ai-answers-work/ | Pipeline mental model, not empirical |
