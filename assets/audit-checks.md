# Deterministic Audit Checks

This file lists all technically verifiable checks. Every check is executable via `WebFetch`, `curl` (Bash), or direct HTML inspection.

**Severity scale:**
- **Critical** — Completely blocks AI discovery or fetch. Must be fixed.
- **High** — Measurably reduces AI visibility based on the evidence.
- **Medium** — Backed by a platform recommendation; effect plausible but not quantified.
- **Low** — Pascal's wager (low cost, no harm, unconfirmed effect).
- **Bonus** — Early-stage specs, relevant only to AI-affine sites.

**Pass criteria:** binary (Pass/Fail) where possible, otherwise Partial with justification.

---

## A — HTTP & Performance (site-wide)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| A-01 | HTTPS enforced | `curl -I http://…` follows 301 to https | 301 → https | Critical | S 🟢 |
| A-02 | HTTP status 200 for root | WebFetch / `curl -I` | 200, max. 1 redirect | Critical | S 🟢 |
| A-03 | Page speed mobile | WebFetch timing or PSI reference in report | < 3s TTFB estimate | High | B 🟡 (Hitches) |
| A-04 | Content-Type header HTML | `curl -I` | `text/html;charset=UTF-8` | Medium | S 🟢 |
| A-05 | No `x-robots-tag: noai/noimageai/noindex` | `curl -I` | Header absent or empty | Critical | S 🟢 |

## B — robots.txt (site-wide)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| B-01 | `/robots.txt` exists | WebFetch | HTTP 200, non-empty | High | S 🟢 |
| B-02 | OpenAI OAI-SearchBot not blocked | Parse robots.txt | no `Disallow: /` for `OAI-SearchBot` | Critical | S 🟢 |
| B-03 | OpenAI GPTBot decision documented | Parse robots.txt | deliberate Allow/Disallow directive (not just default behavior) | Medium | S 🟢 |
| B-04 | Anthropic Claude-SearchBot not blocked | Parse robots.txt | no `Disallow: /` for `Claude-SearchBot` | Critical | S 🟢 |
| B-05 | Anthropic ClaudeBot decision documented | Parse robots.txt | Allow/Disallow set deliberately | Medium | S 🟢 |
| B-06 | Anthropic Claude-User addressed | Parse robots.txt | entry present (Anthropic explicitly recommends this) | Low | S 🟢 |
| B-07 | Google-Extended decision documented | Parse robots.txt | Allow/Disallow set deliberately | Medium | S 🟢 |
| B-08 | Googlebot not blocked | Parse robots.txt | no `Disallow: /` for `Googlebot` | Critical | S 🟢 |
| B-09 | Bingbot not blocked | Parse robots.txt | no `Disallow: /` | Critical | S 🟢 |
| B-10 | PerplexityBot not blocked | Parse robots.txt | no `Disallow: /` | High | S 🟢 |
| B-11 | CCBot decision documented | Parse robots.txt | Allow/Disallow set deliberately | Low | S 🟢 |
| B-12 | `/llms.txt` not disallowed | Parse robots.txt | no `Disallow: /llms.txt` | Medium | S 🟢 |
| B-13 | Subdomain coverage | Check robots.txt of sub-hosts | Anthropic requires a separate file per subdomain | Medium | S 🟢 |

## C — Spec files (site-wide)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| C-01 | `/llms.txt` present | WebFetch | HTTP 200, non-empty | Low (Pascal's wager) | S 🟢 spec; B/adoption 🟡 |
| C-02 | `/llms.txt` Content-Type correct | `curl -I` | `text/plain` or `text/markdown` | Low | S 🟢 |
| C-03 | `/llms.txt` format conformance | Parse content | at least 1 H1, at least 1 H2 with a Markdown link list, URLs preferably `.md` | Low | S 🟢 |
| C-04 | `/sitemap.xml` present | WebFetch | HTTP 200, valid XML | High | S 🟢 |
| C-05 | sitemap.xml contains `<lastmod>` | Parse XML | present on at least one third of URLs | Medium | S 🟢 |
| C-06 | `AGENTS.md` or Vercel alternative | WebFetch the 8 paths (see Vercel spec) | At least 1 path returns 200 with ≥ 2 sections | Low (site) / High (repo) | S 🟢 |
| C-07 | AGENTS.md has drift protection | Inspect content | reference to update ownership or a CI hook is detectable | Low | S 🟢 |
| C-08 | `/sitemap.md` (Vercel) | WebFetch | HTTP 200 | Bonus | S 🟡 |

## D — HTML meta (per page)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| D-01 | `<link rel="canonical">` present | HTML parsing | required, absolute URL | High | S 🟢 |
| D-02 | `<meta name="description">` ≥ 50 characters | HTML parsing | content-length check | Medium | S 🟢 |
| D-03 | OpenGraph tags (og:title, og:description) | HTML parsing | both present | Medium | S 🟢 |
| D-04 | `<html lang="…">` set | HTML parsing | attribute exists, valid ISO code | Medium | S 🟢 |
| D-05 | `hreflang` on multi-language sites | HTML parsing | one link tag per language on i18n sites | Medium | S 🟢 |
| D-06 | Title tag matches the page | HTML parsing | ≥ 10 and ≤ 70 characters | Low | S 🟢 |

## E — HTML semantics (per page)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| E-01 | ≥ 3 section headings (h1–h3) | HTML parsing | count ≥ 3 | High | S 🟢 (Vercel) |
| E-02 | Exactly 1 H1 | HTML parsing | count == 1 | Medium | S 🟢 |
| E-03 | Text-to-HTML ratio ≥ 15% | Measure content vs. markup | ratio check | Medium | S 🟢 (Vercel) |
| E-04 | Lists or tables where appropriate | HTML parsing | heuristic: comparable content rendered as structured elements | Low | B 🟡 |
| E-05 | Essential content readable without JS | Compare raw HTML vs. JS-rendered | at least 60% of visible text in raw HTML | High | S 🟢 |

## F — JSON-LD (per page)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| F-01 | `<script type="application/ld+json">` present | HTML parsing | at least 1 block | Medium *(see note)* | S 🟢 spec; B 🟢🟢 disproves direct AI effect |
| F-02 | JSON-LD is valid JSON | Parser | parses without error | Medium | S 🟢 |
| F-03 | Type appropriate (Article / Product / Organization / FAQPage / HowTo / BreadcrumbList) | Inspect schema type | type present and matches the page kind | Low | S 🟢 |
| F-04 | `dateModified` set | Parse JSON-LD | field exists, ISO date | Medium | S 🟢 |
| F-05 | `BreadcrumbList` present | Parse JSON-LD | block exists | Low | S 🟢 |

**Note on JSON-LD severity:** Communicate explicitly in the audit report: schema improves rich results and the Knowledge Graph (via training) but does **not** affect live AI citation rate (Ahrefs DiD, Tier B 🟢🟢; searchVIU). Severity Medium rather than High because the effect path is indirect.

## G — Markdown mirror (per page, Vercel recommendation)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| G-01 | `.md` suffix works | WebFetch `{url}.md` | HTTP 200, `Content-Type: text/markdown` | Low (Pascal's wager) | S 🟢 recommendation, effect evidence missing |
| G-02 | `<link rel="alternate" type="text/markdown">` | HTML parsing | present | Low | S 🟢 |
| G-03 | Content negotiation | `curl -H "Accept: text/markdown"` | response with `Content-Type: text/markdown` + `Vary: Accept` | Low | S 🟢 |
| G-04 | Frontmatter in the Markdown | Parse Markdown mirror | `title`, `description`, `doc_version`, `last_updated` | Bonus | S 🟢 |
| G-05 | Canonical header in Markdown response | `curl -I` | `Link: <url>; rel="canonical"` | Bonus | S 🟢 |

## H — OpenAPI / code docs (per page, API pages)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| H-01 | OpenAPI link on API pages | HTML parsing on URLs with `/api/`, `/reference/`, `/endpoints/` | link to `openapi.json` / `swagger.json` / `*.yaml` | Medium | S 🟢 (Vercel) |
| H-02 | Code blocks with language tag | Count `<pre><code class="language-…">` | at least 80% of code blocks carry the class | Medium | S 🟢 |
| H-03 | Sample curl calls per endpoint | Scan content | at least 1 curl example per endpoint section | Bonus | S 🟢 |

## I — Crawler telemetry (site-wide, optional)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| I-01 | Bing Webmaster Tools verified | manual question to the user | user confirms | Low | S 🟢 |
| I-02 | IndexNow configured | check API key file `/{key}.txt` | HTTP 200 | Low | S 🟢 |
| I-03 | Cloudflare AI Crawl Control active | manual question to the user | user confirms | Bonus | S 🟢 |

## J — Code repo specific (for GitHub/GitLab repos)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| J-01 | `AGENTS.md` in repo root | WebFetch / Git tree | file present | High | S 🟢 |
| J-02 | AGENTS.md has recommended sections | Inspect content | ≥ 4 of: Project Overview, Build, Test, Code Style, Security, PR Instructions | High | S 🟢 |
| J-03 | Monorepo: subprojects with their own AGENTS.md | Tree inspection | dedicated file under `packages/`, `apps/`, etc. | Medium | S 🟢 |
| J-04 | `CLAUDE.md` / `.cursorrules` / `.cursor/rules` as compatibility layer | Tree inspection | symlink or standalone | Low | S 🟢 |
| J-05 | `/docs` as Markdown present | Tree inspection | docs folder exists | Medium | S 🟢 |

## K — Experimental specs (bonus)

| Code | Check | Method | Pass criterion | Severity | Tier · Confidence |
|---|---|---|---|---|---|
| K-01 | `/.well-known/agent.json` (AWP) | WebFetch | HTTP 200, valid JSON | Bonus | S 🟡 |
| K-02 | `/.well-known/agents.json` (wildcard) | WebFetch | HTTP 200, valid JSON | Bonus | S 🟡 |
| K-03 | `/.well-known/ai-agent.json` (Aiia) | WebFetch | HTTP 200, valid JSON | Bonus | S 🟡 |

**Important:** Always flag K checks as "experimental, no production-grade lever" in the report. Effect evidence = none.

---

## Score formula

**Vercel formula as the base:**
`raw_score = round((passed / total) × 100)`

**Qualitative adjustment:**
- +5 if the qualitative assessment (see `qualitative-rubric.md`) averages ≥ 4.0
- −10 if a Critical-severity fail is present (overrides the pass rate)
- −5 if the hype counter in the audit reveals that the site is optimized for marketing myths (e.g. schema overweight without content substance)

**Tier bands:**
- 90–100 — Excellent
- 70–89 — Good
- 50–69 — Fair
- 0–49 — Needs Improvement

**Important:** The score is **not** a sales argument, it is a diagnosis. When the score is low *because of* bonus or experimental checks, relativize this explicitly in the report.
