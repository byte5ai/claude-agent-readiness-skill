# Audit report template

Binding output format for the audit report. Variables in `{curly braces}`.

**Mandatory rules for every report:**

- Recommendations are taken from `recommendations-library.md` using the **audit wording** (the pre-written 2–4-sentence field), not the R-XX shorthand. R-codes appear at most as a reference tag at the end of a recommendation line.
- Explain insider terms in a half-sentence on first mention (e.g. "JSON-LD (structured data in the page source)…", "canonical link (tells search engines the primary version of a page)…").
- Match the form of address to the user's (informal/formal/neutral). If unclear, stay neutral: "The team should…", "The site needs…".
- Tone: professional, factual, no irony, no insider jokes. The output must be equally readable for engineering, marketing, account management, and external stakeholders.

---

# AI Agent Readiness Audit — {site URL or repo name}

**Audit date:** {YYYY-MM-DD}
**Scope:** {Website / Repo / Both} · **Depth:** {Full scan / Quick}
**Sample:** {number of scanned pages}, by name: {list}
**Knowledge base version:** 2026-05-16

---

## Executive Summary

{3–5 sentences. Concrete: where the site stands, which of the two biggest levers is open, what is surprising.}

**Headline finding:** {1 sentence pinning down the most important point.}

**Score:** {0–100} ({tier band: Excellent / Good / Fair / Needs Improvement})

**Qualitative aggregate score:** {1.0–5.0} ({assessment})

---

## Score-Breakdown

| Area | Pass | Fail | Partial | Skip |
|---|---|---|---|---|
| HTTP & Performance (A) | | | | |
| robots.txt (B) | | | | |
| Spec files (C) | | | | |
| HTML meta (D) | | | | |
| HTML semantics (E) | | | | |
| JSON-LD (F) | | | | |
| Markdown mirror (G) | | | | |
| OpenAPI / code docs (H) | | | | |
| Crawler telemetry (I) | | | | |
| Code repo (J), if applicable | | | | |
| Experimental (K) | | | | |
| **Total** | | | | |

**Raw score:** round((passed / total) × 100) = {value}
**Adjustments:**
- {e.g. "+5 for qualitative assessment 4.2"}
- {e.g. "−10 for Critical fail B-02 (OAI-SearchBot blocked)"}
**Final score:** {value}

---

## Critical findings (fix immediately)

| Finding | Check code | Evidence | Recommendation | Effort |
|---|---|---|---|---|
| {description} | {e.g. B-02} | Tier · Confidence | R-XX | S/M/L |

{If 0 critical findings: "No critical findings — the site is not in acute danger on its technical baseline."}

---

## High-priority findings

| Finding | Check code | Evidence | Recommendation | Effort |
|---|---|---|---|---|
| {…} | {…} | {…} | {…} | {…} |

---

## Medium-/low-priority findings

| Finding | Check code | Evidence | Recommendation | Effort |
|---|---|---|---|---|
| {…} | {…} | {…} | {…} | {…} |

---

## Qualitative assessment

Sample: {n} pages — {page list}

| Aspect | Score | Justification with evidence |
|---|---|---|
| Fact density | {1–5} | {1–2 sentences + quote} |
| Answer-first structure | {1–5} | {…} |
| Statistics density | {1–5} | {…} |
| Expert quotes & attribution | {1–5} | {…} |
| Linguistic clarity | {1–5} | {…} |
| E-E-A-T signals | {1–5} | {…} |
| Answer blocks 50–150 words | {1–5} | {…} |
| Cross-format consistency | {1–5} | {…} |

**Aggregate:** {1.0–5.0}

---

## Recommendations roadmap

### Quick Wins (< 1 person-day)
- {recommendation R-XX, in 1 sentence}

### Mid-term (1–4 weeks)
- {…}

### Strategic Bets (> 1 month)
- {…}

---

## What to skip (hype counter)

This section is **mandatory** and contains at least 2 points. It corrects widespread GEO myths the site owner or their advisors would otherwise likely pursue.

- **{hype myth}**: {what the data says}. {Recommendation: drop or reprioritize.}
- {…}

---

## Trade-off discussion (optional)

{If discussion mode is active: selected axes from `discussion-prompts.md` with options and 1–2 open questions the user should answer.}

---

## Methodology & sources

**Tier distribution of findings:**
- Tier S (platform primary sources): {number of findings based on this}
- Tier A (academic): {number}
- Tier B (practitioner empirics): {number}

**Most important confidence anchors** (relevant for this site):
- {e.g. "Schema null finding Ahrefs DiD 🟢🟢 — basis for R-12 and hype counter Anti-R-01"}

**Limitations of this audit:**
- Sample limited to {n} pages; systematic bias possible.
- Platform algorithms change quarterly; findings have a half-life.
- No active crawler telemetry from Bing Webmaster Tools or Cloudflare AI Crawl Control included — recommendation: the user reviews this directly.
- {further site-specific caveats}

---

## Next steps

{1–3 concrete proposals: "If wanted, I can turn the quick wins into a concrete implementation checklist / a PR patch / a content brief for R-XX."}

---

## Source index

| Tag | Source | URL |
|---|---|---|
| {e.g. "Ahrefs DiD"} | {Ahrefs schema study} | {URL} |
| {…} | {…} | {…} |
