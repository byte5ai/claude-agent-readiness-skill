# Sparring prompts: trade-off discussions

This file supplies questions and discussion frames for the sparring mode. Three main axes, each with a diagnostic question, options, push-back points, and source anchoring.

**Usage rule:** In sparring, never spend more than 2-3 exchanges on a single axis. Once an insight has emerged, move to the next axis or back to audit actions.

---

## Axis 1 - Visibility vs. control

**Diagnostic question:**
A useful diagnostic question is: which matters more to the site owner - that ChatGPT and Claude can cite their content, or that their content does not end up in training datasets?

**Background:** OpenAI and Anthropic separate training, search, and user bots into distinct user agents (Tier S 🟢). Blocking everything by default also disables AI-search visibility. Allowing everything by default enables potential training use of the content.

**Options:**

| Position | What it allows | What it costs | Source |
|---|---|---|---|
| Maximum visibility | All crawlers allowed | Content flows into training | Tier S 🟢 |
| Visibility without training | Only `*-SearchBot` and `*-User` allowed; `GPTBot`/`ClaudeBot`/`Google-Extended`/`CCBot` disallowed | Possibly weaker mid-term representation in model knowledge | Tier S 🟢 |
| Monetization via pay-per-crawl | HTTP 402 for crawlers, Cloudflare AI Crawl Control | High setup effort, small buyer population today | Tier S 🟢 (Cloudflare private beta) |
| Full block | All crawlers disallowed | Complete loss of AI visibility | Tier S 🟢 |

**Push-back points for the sparring role:**
- If the position is to block everything by default: point out that blocking all OpenAI/Anthropic bots also disables ChatGPT Search and Copilot visibility, not just training - these are separate bots.
- If the position is to allow everything by default: surface the question of how the site owner feels about their content flowing into training datasets without direct compensation.
- If pay-per-crawl is on the table: note that the buyer population is currently very small. Worth asking whether this is a real lever today or more of an option to keep open for later.

**Follow-up sub-questions:**
- Which of the three OpenAI bots (training/search/user) should be treated how?
- What is the position on Anthropic? (Same logic, three separate bots.)
- Geographic subdomains - same or different crawler strategy?

---

## Axis 2 - Effort vs. impact (Pascal-wager vs. evidence-based)

**Diagnostic question:**
Useful diagnostics here: what is the time budget for this optimization round - days, weeks, months? And what proof-of-impact standard applies? Is a platform recommendation enough, or does it need to be empirically proven?

**Background:** The recommendations library explicitly separates evidence-based levers, platform recommendations, Pascal-wagers, and hype counters. Spending weeks on schema markup for AI citations optimizes the wrong thing (Tier B 🟢🟢). Spending 10 minutes on `/llms.txt` is a harmless bet.

**Discussion table:**

| Measure | Effort | Evidence status | Worth it? |
|---|---|---|---|
| Answer-first structure (R-01) | M | Tier A 🟢 | Yes, clear lever |
| Statistics & quotes (R-02) | M | Tier A 🟢 | Yes, top lever |
| JSON-LD schema (R-12) | S-M | Tier B 🟢🟢 (no AI-citation effect) | Only for Rich Results |
| Markdown mirrors (R-11) | M-L | Platform recommendation, no impact proof | Pascal-wager territory, effort relatively high |
| `/llms.txt` (R-15) | S | Adoption disputed | Pascal-wager, low cost, OK |
| YouTube strategy (R-07) | L-XL | Tier B 🟢 | Yes, if marketing-relevant |

**Push-back points:**
- If schema is named as a top priority: flag the Ahrefs DiD result - tested with 1,885 pages, no effect on AI citations. Schema is worth doing for other reasons, not for this one.
- If the plan is "tackle everything in parallel": challenge the realism. Working on everything at once means landing nothing properly. The question to surface is which two levers matter most.
- If Markdown mirrors are seen as mandatory: ask where the impact claim comes from. Vercel and Cloudflare recommend them, but no A/B study has been published. At a week of effort, R-01 or R-02 is the better investment.

**Follow-up sub-questions:**
- How many pages does the site have? (Affects effort on per-page measures.)
- Is there engineering capacity, or only a content team?
- What is the quarterly budget for this work?

---

## Axis 3 - Platform diversification vs. focus

**Diagnostic question:**
A useful framing question: which AI surfaces should be the primary optimization target? Google AIO, ChatGPT, Perplexity, Bing Copilot, Claude? Or all at once?

**Background:** The surfaces behave **differently** (Toronto 2025 Tier A 🟢; Ahrefs 15K-overlap study Tier B 🟢):

| Surface | Top-10 overlap with Google | Freshness preference | Dominant sourcing logic |
|---|---|---|---|
| Google AIO | 37.9% | None to slightly against | SEO top-10 + YouTube |
| ChatGPT | 8.6% | Strong (~ 958 days) | Earned media, Reddit, own research |
| Perplexity | 28.6% | Medium (~ 1,166 days) | Topic coverage, domain diversity |
| Gemini | 8.0% | Medium (~ 1,118 days) | (see Google, but weaker top-10 correlation) |
| Bing Copilot | 6.1% | Medium (~ 1,056 days) | Bing index, social signals |

**Implication:** A universal strategy is methodologically questionable. Google AIO optimization means SEO + YouTube. ChatGPT optimization means earned media and fresh content. Doing both is possible, but the priority order has to be explicit.

**Push-back points:**
- If "all AI surfaces" is named as the goal: surface the question of which surface actually drives conversions. Ask whether referral data from Bing Webmaster Tools or Cloudflare logs is available.
- If the focus is SEO-only: note that top-10 SEO captures 37.9% of Google AIO citations but is barely predictive for ChatGPT citations (8.6%). Worth asking whether the Google lever alone is enough.
- If the focus is ChatGPT-only: building earned media over three quarters is slow. Question to surface: is there budget for that, or do shorter paths need to be found?

**Follow-up sub-questions:**
- Is there data on current AI referrals? (Bing Webmaster Tools, Otterly/Profound/Peec, server logs)
- Which customer-acquisition channels are currently strongest?
- Is there a brand that organically drives search volume? (Brand search volume is a strong citation predictor per Digital Bloom 🟡.)

---

## Axis 4 - Code-repo-specific: who is the target audience?

**Diagnostic question (only in code-repo scope):**
A useful framing question: which coding agents should reliably work with the repo? OpenAI Codex, Cursor, Claude Code, Junie, Devin, all of them?

**Background:** AGENTS.md is established as a coding-agent standard (Linux Foundation, > 60k repos). Different tools, however, have their own file conventions (`CLAUDE.md`, `.cursorrules`).

**Options:**

| Position | Measure | Effort | Source |
|---|---|---|---|
| AGENTS.md primary, symlinks for compat | One canonical file + symlinks for `CLAUDE.md`, `.cursorrules` | S | Tier S 🟢 |
| Separate file per tool | AGENTS.md + CLAUDE.md + .cursorrules maintained separately | M (drift risk) | n/a |
| AGENTS.md only, no compat | Modern tools only, older Cursor versions ignore it | S | Tier S 🟢 |

**Push-back points:**
- Ask whether there is a CI hook that checks AGENTS.md alongside releases. Without one, it drifts and delivers wrong context to agents.
- For monorepos: how many subprojects? OpenAI's main repo has 88 AGENTS.md files.

---

## Meta-rule: when to return to audit actions

When the user lands on a clear position in the discussion (e.g. "OK, target is Google AIO + ChatGPT, not Perplexity"), return to the audit and filter the recommendations library against that prioritization. Sparring should not become an end in itself.

When the user introduces a new date or fact in the discussion (e.g. "but study X says..."), ask for the source **before** taking it into a recommendation. If the source is not in the knowledge base and is not plausibly verifiable, treat it as Tier 🔴 until verified.
