# Recommendations library

Each recommendation addresses an audit gap. Sorted by **impact class**, and within each class by **effort ascending**. Uses the tier and confidence tags from the knowledge base.

**Required pattern for every recommendation:**

- **Mechanism** and **In practice** are the internal skill fields (used to decide whether and why the recommendation applies).
- **"Audit phrasing"** is the pre-written text that goes into the audit report **as written**. Self-contained and legible on its own, with half-sentence explanations for insider terms on first mention, 2-4 sentences total. If it doesn't fit (e.g. because the user's team uses a different register or needs specific context), the phrasing is **adjusted, not replaced** - the plain-language depth has to stay intact.
- R-XX codes appear in the report **at most as a reference tag** at the end of a recommendation; they never replace the phrasing itself.

**Effort scale:**
- **S** - < 1 person-day
- **M** - 1-5 person-days
- **L** - 1-4 person-weeks
- **XL** - > 1 person-month or ongoing process overhead

**Impact classes:**
- **Evidence-based lever** - empirically supported (Tier A or Tier B 🟢/🟢🟢)
- **Platform recommendation** - officially recommended by a platform, but with no proof of impact (Tier S 🟢, impact unconfirmed)
- **Pascal-wager** - trivial effort, unproven impact, harmless
- **Hype counter** - something the industry overrates; deliberately NOT to be prioritized

---

## Evidence-based levers (prioritized)

### R-01 - Enforce answer-first structure
**Effort:** M (content audit + refactoring of the top 20 pages) · **Impact:** high
**Mechanism:** Structural filters in the LLM reranking step favor short, directly answering chunks. Consistent with Princeton GEO (Fluency/Easy-to-Understand) and Tier B practitioner findings.
**In practice:** Phrase H2 as a question. The first paragraph after H2 is the direct answer in 1-3 sentences. Justification and context follow below.
**Audit phrasing:** On the most important content pages, phrase H2 headings as concrete user questions and deliver the answer in one to three sentences in the first paragraph after each H2 - context and justification follow below. This produces extractable answer blocks that AI systems can cite directly during retrieval. Marketing statements as H2 ("Digital solutions that create value") are unsuitable for this. (Tier A 🟢, Princeton GEO; Tier B 🟡, practitioner findings)
**Source:** Tier A 🟢 (Princeton GEO), Tier B 🟡 (Hitches Copilot Case)

### R-02 - Build in statistics and expert quotes
**Effort:** M (ongoing editorial work) · **Impact:** high
**Mechanism:** Two of the three top levers from Princeton GEO. Quotation Addition and Statistics Addition show the highest citation lifts in the KDD-2024 setup.
**In practice:** Per content page, at least one concrete statistic (with date + source) and one attributed expert quote (name, affiliation, link if possible). Fabricated numbers are counter-productive (citation loss through the earned-media-bias mechanism).
**Audit phrasing:** On every relevant content page, include at least one concrete, dated statistic with a primary source and one expert quote attributed by name (a person with affiliation, ideally linked to the original source). In a controlled study, these two elements rank among the three strongest citation levers in LLM answers - abstract marketing claims without numbers or attribution are cited far less often. Fabricated or unverified numbers are counter-productive, because the earned-media-bias mechanism penalizes inconsistencies with other sources. (Tier A 🟢, Princeton GEO)
**Source:** Tier A 🟢 (Princeton GEO)

### R-03 - Inline citations to primary sources
**Effort:** S-M · **Impact:** high
**Mechanism:** Princeton GEO "Cite Sources" as a top lever; Toronto 2025 shows systematic earned-media bias.
**In practice:** Link directly to studies, official platform docs, datasets. No SEO-aggregator links as a primary source.
**Audit phrasing:** Back up claims in the content with inline links directly to primary sources - original studies, official platform documentation, datasets, government publications. SEO-aggregator or listicle links as a "source" are unsuitable, because LLMs show a measurable bias toward verifiable, third-party-corroborated content. Concretely: for each main claim in an article, set a direct link to the original source, not to an internal topic overview page. (Tier A 🟢, Princeton GEO; Tier A 🟢, Toronto 2025)
**Source:** Tier A 🟢 (Princeton, Toronto)

### R-04 - Content freshness, targeted for ChatGPT/Perplexity/Copilot
**Effort:** M (editorial process) · **Impact:** high for ChatGPT/Perplexity/Copilot, **zero for Google AIO**
**Mechanism:** The Ahrefs 17M study shows a clear preference for newer content on all surfaces except Google AIO.
**In practice:** For pages relevant to these surfaces, set a visible `dateModified` plus real content updates (no pure date-bumping - that gets flagged as deception). Google AIO actually prefers slightly older content - do not artificially "refresh" there.
**Audit phrasing:** For pages intended to be cited in ChatGPT, Perplexity, or Microsoft Copilot, maintain a visible update date (`dateModified` in the markup and at the top of the page) and pair it with real content updates - new numbers, new examples, additional sections. An analysis of 17 million AI citations shows that these systems prefer newer content, while Google AI Overview prefers slightly older content. Pure "date-bumping without content changes" is detected by the models as deception and brings no benefit. (Tier B 🟢, Ahrefs 17M study)
**Source:** Tier B 🟢 (Ahrefs 17M)

### R-05 - Keep SEO top-10 as a baseline (but do not overrate it)
**Effort:** L (ongoing) · **Impact:** necessary but not sufficient condition for Google AIO
**Mechanism:** 37.9% of AIO citations come from the top-10 (Ahrefs March 2026 correction, previously claimed at 76%). For ChatGPT/Perplexity/Gemini, top-10 is not even necessary.
**In practice:** Do not abandon SEO, but do not sell it as a cure-all either. Invest resources primarily in content substance rather than SERP position.
**Audit phrasing:** Classic SEO top-10 positions (organic Google top-10 rankings for relevant queries) remain a necessary baseline for Google AI Overview, but they are not a cure-all and are not even necessary for ChatGPT, Perplexity, or Gemini: only around 38% of AIO citations come from the top 10 of the respective SERP. The team should continue SEO work but deliberately shift resources into content substance (statistics, quotes, answer structure) rather than pure ranking optimization. (Tier B 🟢, Ahrefs)
**Source:** Tier B 🟢 (Ahrefs)

### R-06 - Build earned media deliberately
**Effort:** L-XL · **Impact:** high (systematic bias)
**Mechanism:** Toronto 2025 shows: LLMs favor verifiable, attributable, third-party-corroborated content. Brand search volume correlates more strongly with citations than backlinks do (Digital Bloom, Tier B 🟡).
**In practice:** PR, guest contributions in relevant industry media, study mentions in trade articles. Owned content + earned mentions in combination.
**Audit phrasing:** Build earned media (editorial mentions and links on third-party sites that are not your own) deliberately - through PR, guest contributions in industry media, study mentions in trade articles, and through brand awareness. LLMs show a systematic bias toward third-party-corroborated content, and brand search volume correlates more strongly with AI citations than raw backlink counts. Owned content alone is rarely enough - the combination with external confirmation is the actual lever. (Tier A 🟢, Toronto 2025; Tier B 🟡, Digital Bloom)
**Source:** Tier A 🟢 (Toronto), Tier B 🟡 (Digital Bloom)

### R-07 - Build YouTube representation
**Effort:** L-XL · **Impact:** high (for Google AIO and Bing Copilot)
**Mechanism:** YouTube is the most-cited domain in Google AIO (Ahrefs March 2026); 18.2% of off-rank citations are YouTube.
**In practice:** Video versions of the most important content pages, with transcripts and thematically consistent titles/descriptions. Not "YouTube as SEO afterthought" but a standalone content layer.
**Audit phrasing:** Build a YouTube presence with video versions of the most important content pages - with full transcripts and thematically consistent titles and descriptions that match the terminology used on your own website. Per Ahrefs analysis, YouTube is the most-cited domain in Google AI Overview; around 18% of cited sources outside the classic SERP top-10 come from there. Video should be treated as a standalone content layer, not as mere repackaging. (Tier B 🟢, Ahrefs)
**Source:** Tier B 🟢 (Ahrefs)

### R-08 - Reduce JS rendering dependency
**Effort:** M-L (depending on stack) · **Impact:** high if currently poor
**Mechanism:** AI crawlers generally do not render JS. SSR/SSG/pre-rendering is a prerequisite for being fetchable at all.
**In practice:** Next.js/Astro/Nuxt SSR or SSG for all content pages. SPAs for pure applications, not for marketing or documentation content.
**Audit phrasing:** Deliver all marketing and content pages so that the full primary content is already present in the initial HTML - that is, via server-side rendering or static generation (SSR/SSG, e.g. Next.js, Astro, Nuxt) rather than via pure client-side JavaScript. AI crawlers generally do not execute JavaScript; content that is only loaded later via JS is simply not present for them. Single-page-application patterns belong in internal applications, not in public content areas. (Tier S 🟢, Vercel and OpenAI documentation)
**Source:** Tier S 🟢 (Vercel, OpenAI)

### R-09 - Maintain sitemap.xml with `<lastmod>`
**Effort:** S · **Impact:** medium
**Mechanism:** Discoverability baseline; current `lastmod` values help all crawlers with prioritization.
**Audit phrasing:** Provide a `sitemap.xml` (a machine-readable list of all relevant URLs on a site), reference it correctly in robots.txt, and maintain a current `<lastmod>` value for each URL that is automatically updated on real content changes. Current `lastmod` values help both classic search crawlers and AI crawlers set sensible recrawl priorities. Outdated or missing `lastmod` values are not a killer, but they are an avoidable weakness. (Tier S 🟢, platform standard)
**Source:** Tier S 🟢

### R-10 - Set a deliberate crawler strategy
**Effort:** S · **Impact:** critical if currently misconfigured
**Mechanism:** robots.txt must not block OAI-SearchBot, Claude-SearchBot, Googlebot, Bingbot, PerplexityBot - otherwise no surface access. Training bots (GPTBot, ClaudeBot, Google-Extended, CCBot) are a separate decision.
**In practice:** Set explicit per-bot directives instead of relying on default behavior. Check subdomain coverage.
**Audit phrasing:** In `robots.txt`, set explicit per-AI-crawler rules rather than relying on default behavior. Search crawlers like `OAI-SearchBot` (ChatGPT answers), `Claude-SearchBot`, `PerplexityBot`, `Googlebot`, and `Bingbot` must be allowed - otherwise the site is invisible on the corresponding AI surfaces. Training crawlers like `GPTBot`, `ClaudeBot`, `Google-Extended`, or `CCBot` are a separate, deliberate decision (allowing them means content flows into model training). Check coverage on all subdomains, not just the main domain. (Tier S 🟢, OpenAI and Anthropic documentation)
**Source:** Tier S 🟢 (OpenAI, Anthropic)

---

## Platform recommendations (medium priority, impact unconfirmed)

### R-11 - Markdown mirror of the top 50 content pages
**Effort:** M-L · **Impact:** unconfirmed
**Mechanism:** Vercel and Cloudflare recommend it; theoretically reduces parsing overhead for LLM fetchers. No public A/B study available.
**In practice:** `.md`-suffix routing, `Content-Type: text/markdown`, `<link rel="alternate" type="text/markdown">`, frontmatter with title/description/date/version.
**Audit phrasing:** Provide a Markdown mirror version of the top 50 content pages or so (parallel delivery of the same content under a `.md` URL with `Content-Type: text/markdown` and a `<link rel="alternate" type="text/markdown">` reference in the HTML). Vercel and Cloudflare explicitly recommend this step, because Markdown is easier for LLM fetchers to parse than HTML - a public A/B impact proof, however, is still missing. Sensible as an experimental investment; should not be sold as a guaranteed lever. (Tier S 🟢, Vercel and Cloudflare recommendation; impact proof open)
**Source:** Tier S 🟢 (Vercel, Cloudflare) - but impact proof is missing

### R-12 - Structured data (Schema.org JSON-LD)
**Effort:** S-M · **Impact:** no direct AI-citation effect (Ahrefs DiD 🟢🟢), but indirect benefit via Rich Results / Knowledge Graph / Voice
**Mechanism:** Schema influences Google's understanding of the entity (training), not the live AI fetch.
**In practice:** Article, FAQPage, HowTo, Product, Organization, Person, BreadcrumbList depending on page type. Maintain `dateModified`.
**Audit phrasing:** Maintain Schema.org markup as JSON-LD (structured data in the page source that explains content type and entities to machines) according to page type - Article, FAQPage, HowTo, Product, Organization, Person, BreadcrumbList - and keep the `dateModified` field synchronized with real content updates. This measure serves Rich Results in classic Google search and entity maintenance in the Knowledge Graph; it does not directly increase the AI citation rate: a controlled study over nearly 6,000 pages shows no measurable citation effect from pure schema addition. The team should therefore implement schema, but should not communicate it as an AI-visibility lever. (Tier S 🟢, schema.org; Tier B 🟢🟢, Ahrefs DiD study on AI impact)
**Source:** Tier S 🟢 (schema.org); Tier B 🟢🟢 (Ahrefs schema null result for direct AI citations)
**Explicit in the report output:** "We recommend schema for Rich Results, *not* as an AI-citation booster."

### R-13 - Bing Webmaster Tools + IndexNow
**Effort:** S · **Impact:** visibility on the Bing side (including Copilot)
**In practice:** Verification, IndexNow API key for push indexing on updates.
**Audit phrasing:** Verify the site in Bing Webmaster Tools and integrate IndexNow (an open push-indexing protocol from Microsoft/Yandex that actively reports content changes to search engines) via API key. Bing is the data basis for Microsoft Copilot and ChatGPT Search fallbacks; faster indexing there can accelerate visibility. Effort is low; the impact case is plausible but not hard-quantified. (Tier S 🟢, Bing AI Performance documentation; Tier B 🟡, Hitches Case Study)
**Source:** Tier S 🟢 (Bing AI Performance), Tier B 🟡 (Hitches Case Study)

### R-14 - Cross-format consistency for entities
**Effort:** M (ongoing) · **Impact:** plausible, not quantified
**In practice:** Use the same names for products, concepts, and people across text, alt text, video titles, and transcripts.
**Audit phrasing:** Spell product names, technical terms, and person mentions identically across all content formats - i.e. the same spellings in body copy, image alt texts (descriptive text for images that crawlers and screen readers read), video titles, transcripts, and social posts. Consistent naming stabilizes entity resolution in the Knowledge Graph components of search engines and LLMs, so the same brand or concept is recognized as a single entry. Impact is plausible but not hard-quantified. (Tier S 🟢, Bing AI Performance documentation)
**Source:** Tier S 🟢 (Bing AI Performance)

---

## Pascal-wager (low cost, low risk, impact unconfirmed)

### R-15 - Create a `/llms.txt`
**Effort:** S (10-30 min for a medium-sized site) · **Impact:** unproven
**Mechanism:** Spec by Howard Sep 2024, recommended by Vercel and Cloudflare. Google explicitly says: not needed. Adoption by LLM vendors not publicly confirmed.
**In practice:** Root path, H1 with site name, H2 sections with Markdown link lists to the most important pages. URLs preferably with `.md` extension if a Markdown mirror exists.
**Recommendation:** If implemented, keep it lean and current. **Do not sell it as an AI-visibility lever.**
**Audit phrasing:** A `/llms.txt` file (a proposed but non-binding Markdown file at the site root that gives LLM crawlers a curated overview of the most important pages) can be created with minimal effort: H1 with site name, H2 sections with link lists to core content, ideally with `.md` URLs if a Markdown mirror exists. Important: Google officially says the file is not needed, and there is no publicly confirmed adoption proof from LLM vendors. If the team creates `/llms.txt`, keep it lean and current - but in client or stakeholder reporting, do not position it as an AI-visibility lever; treat it as a cheap experimental bet. (Tier S 🟢, spec; Tier S 🟢, Google statement "not needed"; Tier D, HN skepticism)
**Source:** Tier S 🟢 (spec), Tier S 🟢 (Google says not needed), Tier D HN skepticism

### R-16 - `AGENTS.md` for websites (Vercel extension)
**Effort:** S · **Impact:** unproven
**Mechanism:** AGENTS.md is designed for code repos; Vercel also accepts it in the web root as a "skill file".
**In practice:** If created, with drift protection (CI hook that updates AGENTS.md alongside site releases) - otherwise it delivers wrong information to agents with an authority signal.
**Audit phrasing:** An `AGENTS.md` at the site root (originally a convention for code repos, extended by Vercel as a "skill file" for websites) can be created to give AI agents structured hints about the site. If implemented, a CI hook must check on every release that the file does not drift from the site's current state - an outdated `AGENTS.md` delivers wrong information to agents with an authority signal and is more harmful than no file at all. Adoption by actual AI agents is still unproven; treat this as a Pascal-wager, not a mandatory measure. (Tier S 🟢, spec for repos; Vercel extension for sites unproven)
**Source:** Tier S 🟢 (spec for repos), Vercel extension for sites unproven

### R-17 - Sitemap.md (Vercel recommendation)
**Effort:** S · **Impact:** unproven
**Audit phrasing:** A `sitemap.md` (a Markdown variant of the classic XML sitemap, proposed by Vercel as a complementary format) can be provided alongside `sitemap.xml` by exporting the most important URLs as a Markdown link list. Effort is low; a public impact proof is missing - treat as an experimental complement, not as a lever. The XML sitemap remains the mandatory standard. (Tier S 🟡, Vercel recommendation)
**Source:** Tier S 🟡 (Vercel)

---

## Hype counter (do NOT prioritize)

### Anti-R-01 - Selling schema markup as an AI-citation lever
**Finding:** Ahrefs DiD (Tier B 🟢🟢, n=1,885 + 4,000 controls) shows no measurable effect on AIO/AI Mode/ChatGPT citations after schema addition. searchVIU: 5 AI systems do not even read JSON-LD during live retrieval.
**Audit phrasing:** Schema.org markup (structured data as JSON-LD in the page source) should not be sold as a measure to increase the AI citation rate. A controlled study with 1,885 test pages and around 4,000 comparison pages shows no measurable effect on citations in Google AI Overview, ChatGPT, or Google AI Mode from pure schema addition; a parallel live test confirms that the major AI systems ignore JSON-LD during direct fetch. Schema remains useful for classic Rich Results and Knowledge Graph maintenance - not for AI visibility. (Tier B 🟢🟢, Ahrefs DiD; Tier B 🟡, searchVIU)
**Recommendation:** Implement schema for Rich Results, **not** for AI citations. Communicate this explicitly in the report if the user wants to optimize in that direction.

### Anti-R-02 - Selling llms.txt as a mandatory measure
**Finding:** Google says in the official guide: not needed. Adoption by LLM vendors at inference time is not publicly confirmed. Pascal-wager territory, not a lever.
**Audit phrasing:** A `/llms.txt` (a proposed Markdown file at the site root intended to give LLM crawlers a curated content overview) should not be sold as a mandatory measure or guaranteed AI-visibility lever. Google explicitly confirms in the official guide that the file is not needed, and no relevant LLM platform has publicly confirmed evaluating it at inference time. The file can optionally be created (see R-15) - but as an experimental, low-cost bet, not as a priority alongside real levers. (Tier S 🟢, Google statement; Tier D, community skepticism)
**Recommendation:** R-15 (creating it is fine), but mark it as "experimental", not as a priority.

### Anti-R-03 - "Update everything frequently" as a blanket rule
**Finding:** The average age of cited content is 2.9 years (Ahrefs 17M). Google AIO actually prefers slightly older content. Pure date-bumping without content updates is detected as deception.
**Audit phrasing:** The team should skip the blanket recommendation to "regularly bump all content to a new date". An analysis of 17 million AI citations shows an average age of cited content of around 2.9 years; Google AI Overview actually prefers slightly older content. Pure updating of the `dateModified` field without a content change is detected by the models as deception and tends to weaken visibility. Use freshness deliberately where it works (ChatGPT, Perplexity, Microsoft Copilot - see R-04), not across the board. (Tier B 🟢, Ahrefs 17M study)
**Recommendation:** Apply fresh updates deliberately for ChatGPT/Perplexity/Copilot (see R-04), but do not bump everything indiscriminately.

### Anti-R-04 - Programmatic SEO as a scaling lever
**Finding:** Otterly n=1: 4.8% of Copilot citations from programmatic pages, 74.6% from 5 top pages. Power-law distribution dominates, not volume.
**Audit phrasing:** The team should not plan programmatic SEO (the automated generation of thousands of thematically similar landing pages from templates and databases) as a scaling lever for AI citations. In an Otterly analysis, only around 5% of Copilot citations came from programmatic pages, while almost 75% of citations were concentrated on just five top pages. The distribution follows a power-law: a few content-strong pages dominate; sheer volume brings almost nothing. Investing in a few deep pages beats scaling thin templates. (Tier B 🟡, Otterly n=1)
**Recommendation:** A few strong pages > thousands of thin pages.

### Anti-R-05 - "47% more citations from tables" marketing numbers
**Finding:** No verifiable primary source.
**Audit phrasing:** Circulating marketing claims like "47% more AI citations from tables" or similar precise-percentage promises are not robust - a verifiable primary source does not exist. The team should not reproduce such pseudo-precision in its own reporting or in client material. If a stakeholder cites such numbers, ask politely for the original study before turning it into an action. (Tier D, no primary source)
**Recommendation:** Avoid such pseudo-precision in your own output. If a user brings such a number, politely ask for the primary source.

### Anti-R-06 - Implementing experimental specs (agent.json, agents.json, ai-agent.json, PAM)
**Finding:** Early-stage drafts, no vendor adoption, no impact documented.
**Audit phrasing:** Experimental specifications like `agent.json`, `agents.json`, `ai-agent.json`, or the PAM protocol (various competing early-stage drafts intended to give AI agents metadata about a website) should not be implemented. None of these specs has confirmed vendor adoption from the major LLM platforms, and there is no documented impact on visibility or citations. Save the effort - one exception is possible for strongly AI-affine sites where the implementation itself is desirable as a marketing signal ("we are an early adopter"). (Tier D, early-stage specs without adoption)
**Recommendation:** Only worth playing for AI-affine sites with a marketing bonus - otherwise save the effort.

---

## Selection rule for the report

If the audit produces more than 8 findings, **prioritize in the report** according to this scheme:

1. **Critical-severity findings** first (regardless of impact class).
2. Then **evidence-based levers** with low effort (R-01 to R-10, filtered on S/M effort).
3. Then **platform recommendations** if the site runs on Vercel/Cloudflare (otherwise omit).
4. **Pascal-wagers** as a "Quick wins" appendix.
5. **Hype counters** as a separate section "What you can skip" - always at least 2 points, because the user likely arrives with hype expectations.
