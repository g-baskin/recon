---
name: recon
description: Run competitive intelligence reconnaissance on targets — tears through websites, APIs, tech stacks, communities, infrastructure, domains, IPs, emails, open ports, and stores findings in the project
user-invocable: true
argument-hint: "[--quick|--deep] [--phases 1,2,3] [--format md|json|csv] [--diff] <target URLs or product names>"
allowed-tools: Bash, Read, Write, Glob, Grep, WebFetch, WebSearch, Task
---

# Recon — Deep Competitive Intelligence & Build Protocol

> **Author:** Greg Baskin ([github.com/g-baskin](https://github.com/g-baskin))
> **License:** GPL-3.0

When the user mentions websites, tools, software, products, or competitors they want investigated — or says anything like "run the protocol," "research this," "tear this apart," "investigate these," "act as a hacker," or names specific URLs/products to analyze — execute this pipeline. **Do NOT summarize early. Go deep on every phase before moving to the next.**

Use parallel agents (Task tool with subagent_type=general-purpose or Explore) to maximize throughput. Launch multiple research agents simultaneously for each target.

## Trigger Phrases

Activate this protocol when the user says any of:
- "Run the protocol on [targets]"
- "Research these" / "Investigate these" / "Look into these"
- "Act as a hacker" / "Tear this apart" / "Tear their product apart"
- "How do they work?" / "What are they using?"
- "Can we build something like this?"
- "Leave no stone unturned"
- Or names specific URLs/products/tools/software to analyze

---

## FLAGS & OPTIONS

Parse the user's input for these flags. If not provided, use defaults.

### Depth Control

| Flag | Behavior | Default |
|------|----------|---------|
| `--quick` | Phase 1 only, surface-level scan. Business overview, basic tech stack, top competitors. Skip deep OSINT, security, and IP phases. | No |
| `--deep` | Full 5-phase pipeline. Leave no stone unturned. | Yes (default) |
| (no flag) | Full 5-phase pipeline | Yes |

### Phase Selection

| Flag | Behavior | Example |
|------|----------|---------|
| `--phases 1,2` | Run only specified phases | `--phases 1,2` for research only |
| `--phases 3,4,5` | Run only specified phases | `--phases 3,4,5` for build hardening |
| (no flag) | Run all phases (or phase 1 only if `--quick`) | All 5 phases |

### Output Format

| Flag | Behavior | Default |
|------|----------|---------|
| `--format md` | Markdown report (default) | Yes |
| `--format json` | Structured JSON output | No |
| `--format csv` | CSV tables for spreadsheet import | No |
| (no flag) | Markdown | Yes |

### Diff Mode

| Flag | Behavior |
|------|----------|
| `--diff` | Compare against previous recon report for the same target. Highlight what changed: new funding, team growth, tech stack shifts, feature additions/removals, pricing changes. |

---

## TARGET TYPE DETECTION

Before starting research, auto-detect the target type and adjust the research approach:

| Target Type | Detection Signals | Adjusted Approach |
|-------------|-------------------|-------------------|
| **SaaS Product** | Has pricing page, login/signup, app subdomain | Full pipeline — pricing analysis, user reviews, API docs |
| **Open Source Project** | GitHub repo, no pricing, community-driven | Focus on GitHub stars/forks, contributors, ecosystem, adoption |
| **API Service** | Developer docs, API keys, SDKs | Focus on API design, rate limits, pricing per call, DX |
| **Mobile App** | App Store / Play Store listing | Include app store reviews, download metrics, mobile tech stack |
| **CLI Tool** | npm/pip/brew install, terminal-focused | Focus on GitHub, package registry stats, CLI comparison |
| **Hardware/IoT** | Physical product, shipping | Include supply chain, manufacturing, certifications |
| **Marketplace/Platform** | Two-sided (buyers + sellers) | Analyze both sides, network effects, take rate |

Apply the detected type throughout all phases to ask the right questions and skip irrelevant ones (e.g., don't search App Store reviews for a CLI tool).

---

## OUTPUT & FILE STORAGE

**All findings MUST be written to files, not just printed to chat.**

### File Structure

For each recon run, create a report directory in the current project:

```
recon-reports/
├── [target-name]/
│   ├── RECON_REPORT.md          # Full structured report (markdown)
│   ├── RECON_REPORT.json        # Structured data (if --format json)
│   ├── comparison.csv           # Comparison matrix (if --format csv or multiple targets)
│   ├── metadata.json            # Run metadata (date, flags, phases, target type)
│   └── previous/               # Previous reports (for --diff mode)
│       └── RECON_REPORT_2026-02-24.md
└── COMPARISON_MATRIX.md         # Cross-target comparison (if multiple targets)
```

### metadata.json Format

```json
{
  "target": "https://competitor.com",
  "target_name": "competitor",
  "target_type": "saas",
  "run_date": "2026-02-24T19:30:00Z",
  "flags": {
    "depth": "deep",
    "phases": [1, 2, 3, 4, 5],
    "format": "md",
    "diff": false
  },
  "phases_completed": [1, 2, 3, 4, 5],
  "previous_runs": ["2026-01-15", "2025-12-01"]
}
```

### Report Writing Rules

1. **Always write to file first**, then print a summary to chat
2. Chat summary should be a condensed version (key findings, top recommendations, comparison highlights) with a pointer to the full report file
3. If `--format json` is specified, write both `.md` and `.json` versions
4. If `--format csv` is specified, write `.md` plus `.csv` tables for key data (pricing, features, tech stack)
5. For `--diff` mode: check `recon-reports/[target]/previous/` for prior reports, move current report to `previous/` with date suffix, then write new report and a `DIFF_REPORT.md` highlighting changes

### Chat Summary Template

After writing the full report, print to chat:

```
## Recon Complete: [Target Name]

**Target Type:** [SaaS / Open Source / API / etc.]
**Phases Run:** [1-5]
**Depth:** [Quick / Deep]

### Key Findings
- [Top 3-5 bullet points]

### Top Recommendations
- [CRITICAL] ...
- [HIGH] ...

### Full report saved to:
`recon-reports/[target-name]/RECON_REPORT.md`
```

---

## COMPARISON MATRIX (Multiple Targets)

When 2+ targets are provided, auto-generate a comparison matrix after all individual reports are complete.

### Comparison Dimensions

| Dimension | What to Compare |
|-----------|-----------------|
| **Product** | Core features, unique selling points, target audience |
| **Pricing** | Free tier, paid tiers, enterprise, per-seat vs. flat |
| **Tech Stack** | Frontend, backend, database, infrastructure |
| **Team** | Size, funding, key hires, growth signals |
| **Community** | GitHub stars, Reddit mentions, Discord size, NPS signals |
| **Gaps** | User complaints, missing features, pain points |
| **Moat** | What's hard to replicate, switching costs, network effects |
| **Opportunity** | Where we can differentiate, underserved segments |

### Output Format

Write to `recon-reports/COMPARISON_MATRIX.md`:

```markdown
# Competitive Comparison Matrix

| Dimension | Target A | Target B | Target C | Our Opportunity |
|-----------|----------|----------|----------|-----------------|
| Core Product | ... | ... | ... | ... |
| Pricing | ... | ... | ... | ... |
| Tech Stack | ... | ... | ... | ... |
| Team Size | ... | ... | ... | ... |
| Funding | ... | ... | ... | ... |
| Community | ... | ... | ... | ... |
| Key Gap | ... | ... | ... | ... |
| Moat | ... | ... | ... | ... |

## Detailed Analysis
[Narrative comparison with recommendations]
```

---

## DIFF MODE (Re-run & Compare)

When `--diff` is used, or when a previous report exists for the same target:

### Diff Process

1. Check `recon-reports/[target]/` for existing `RECON_REPORT.md`
2. If found, move it to `recon-reports/[target]/previous/RECON_REPORT_[date].md`
3. Run the full recon as normal
4. Compare new findings against previous report
5. Generate `DIFF_REPORT.md` highlighting changes

### Change Categories

| Category | What Changed | Signal |
|----------|-------------|--------|
| **Funding** | New round, acquisition, revenue milestone | Growth / pivot |
| **Team** | Key hires, departures, new roles posted | Expansion / contraction |
| **Product** | New features, removed features, pricing changes | Strategy shift |
| **Tech Stack** | New frameworks, infrastructure changes | Technical investment |
| **Community** | Growth/decline in mentions, sentiment shift | Market position |
| **Competitive** | New competitors entered, others exited | Market dynamics |

### Diff Report Format

```markdown
# Recon Diff: [Target Name]
**Previous scan:** [date]
**Current scan:** [date]

## Changes Detected

### New
- [Things that didn't exist before]

### Changed
- [Things that were different before] (was: X, now: Y)

### Removed
- [Things that existed before but are gone]

### Unchanged
- [Key things that stayed the same — stability signals]
```

---

## PHASE 1 — FULL RECONNAISSANCE (Leave No Stone Unturned)

Act as a competitive intelligence analyst with hacker-level curiosity. For EACH target, research ALL of the following:

**Business Intelligence:**
- What is the product? What problem does it solve? Who is the target user?
- Unique selling proposition — how do they position against competitors?
- Pricing model — free tier, paid tiers, enterprise? Any public revenue/sales data?
- User base size — public metrics, social proof, testimonials, case studies?
- Funding history — Crunchbase, PitchBook, AngelList, press releases
- Team — who built it? LinkedIn profiles of founders/key engineers. Background?
- Company entity — incorporation records, any public filings
- Growth trajectory — are they growing, stagnant, or declining?

**Web Presence & Footprint:**
- Main site structure — sitemap, all public pages, blog, docs, changelog
- Wayback Machine (web.archive.org) — how has the site evolved? Features added/removed? Pivots?
- Social media — Twitter/X, LinkedIn, Reddit mentions, Product Hunt launches, Hacker News threads
- Community — Discord, Slack, forums, subreddits where users discuss it
- Reviews — G2, Capterra, Trustpilot, App Store, any review aggregator
- SEO footprint — what keywords do they rank for? Content strategy?
- Public API documentation, developer docs, integration guides
- Job postings — what roles are they hiring for? (reveals tech stack and growth areas)
- Press coverage — TechCrunch, Product Hunt, blog features, podcast appearances

**Competitive Landscape:**
- Direct competitors — who else does this exact thing?
- Indirect competitors — adjacent tools that overlap
- Alternatives mentioned by users in forums, Reddit, comparison articles
- User complaints — what's missing? Where are the gaps? What do people hate?
- Switching costs — how hard is it to leave their product?

**Research Sources (use in parallel — leave no stone unturned):**

*Core platforms:*
- WebSearch for each target name + variations
- WebFetch on their main site, about page, pricing page, docs, blog
- WebFetch on Wayback Machine snapshots: `https://web.archive.org/web/*/targeturl.com`
- WebSearch for "[target] review," "[target] alternative," "[target] funding"
- GitHub search for their org name, repos, SDKs, open-source components
- Reddit search for mentions in relevant subreddits
- Hacker News — search via WebSearch for `site:news.ycombinator.com [target]`
- Twitter/X — search for mentions, founder accounts, community sentiment
- LinkedIn — founder profiles, team size, job postings, company page

*Community & forums:*
- Indie Hackers — founder stories, milestone posts, revenue transparency
- Product Hunt — launch history, upvotes, maker comments, featured collections
- Discord servers — many products have public Discords with real user feedback
- Slack communities — industry-specific groups (Demand Curve, SaaS Twitter, etc.)
- StackOverflow — questions about their API/SDK, developer adoption signals

*Review & comparison sites:*
- G2, Capterra, Trustpilot — verified user reviews and ratings
- GetLatka — SaaS revenue/metrics database
- SimilarWeb — traffic estimates, referral sources, growth trends
- Crunchbase / PitchBook / Wellfound (AngelList) — funding, team, investors

*Tech stack detection:*
- BuiltWith / Wappalyzer — tech stack fingerprinting
- npm / PyPI / crates.io / Docker Hub — published packages and libraries
- WHOIS / DNS records — domain age, hosting provider, infrastructure changes
- SSL certificate transparency logs — subdomain discovery

*Content & media:*
- YouTube — conference talks, demos, founder interviews, tutorials
- Medium / Substack / dev.to — founder blog posts, technical write-ups
- Podcasts (search Spotify, Apple Podcasts) — founder interviews
- SlideShare / Speaker Deck — architecture talks, pitch decks

*Team & culture intel:*
- Glassdoor / Levels.fyi — salaries, culture reviews, internal complaints
- Wellfound (AngelList) — startup profiles, team bios, open roles
- LinkedIn job postings — reveals tech stack, growth areas, priorities

*Post-mortem & case study sites:*
- Failory — startup post-mortems, failure analysis
- Startup Obituary — shutdown announcements, cause of death
- Starter Story — detailed founder interviews with revenue numbers
- Famewall — customer testimonial case studies

*Mobile (if applicable):*
- App Store / Play Store — reviews, ratings, download estimates, version history

**Quick Mode (--quick):** For phase 1 in quick mode, cover only:
- Product overview (1-2 paragraphs)
- Pricing summary
- Top 3 competitors
- Basic tech stack (from job postings or obvious signals)
- Skip: Wayback Machine, deep community research, OSINT, funding deep-dive

---

## PHASE 2 — TECHNICAL REVERSE ENGINEERING (Tear It Apart)

Act as a senior engineer doing a full technical teardown. For EACH target:

**Public Code & Open Source:**
- GitHub repos — official org, any open-source components, SDKs, API clients
- npm / PyPI / crate packages — do they publish libraries?
- Open-source contributions by their team members
- Any forks or community-maintained tools built on top of them
- License types on any public repos

**Tech Stack Detection:**
- Frontend — framework (React, Next.js, Vue, Svelte?), component libraries, CSS approach
- Backend — clues from API responses, headers, error messages, job postings
- Infrastructure — cloud provider, CDN, status pages (e.g., status.targetname.com)
- Database — hints from docs, schema references, API patterns, job postings
- Third-party integrations — payment processor, analytics, auth provider, email service
- Analyze headers, meta tags, script sources from their site

**API & Data Flow:**
- Public API endpoints — documented and undocumented
- Request/response patterns — data structures, pagination, filtering
- Authentication method — OAuth, API keys, JWT, sessions?
- Rate limiting, webhook patterns
- GraphQL schemas, OpenAPI/Swagger specs if available
- Try reading: `[target]/api`, `[target]/docs/api`, `[target]/swagger.json`, `[target]/graphql`

**Architecture Patterns:**
- How does data flow through the system?
- What's the core algorithm/logic that makes it work?
- What would be the hardest part to replicate?
- What's commodity (auth, payments, CRUD) vs. truly novel?

**Open Source Intelligence (OSINT):**
- Developer blog posts explaining architecture decisions (Medium, Substack, dev.to)
- Conference talks by their engineers (YouTube, SlideShare, Speaker Deck)
- Technical write-ups, postmortems, architecture diagrams
- Dependencies in any public package files (package.json, requirements.txt, etc.)
- BuiltWith, Wappalyzer, or similar for tech stack fingerprinting
- DNS records, SSL certificate info, WHOIS data
- npm / PyPI / crates.io / Docker Hub — published packages
- Podcast appearances by founders or engineers (Spotify, Apple Podcasts)
- Glassdoor / Levels.fyi — job postings reveal internal tech stack and priorities
- Indie Hackers / Starter Story — founders sometimes share full tech stack details
- SSL certificate transparency logs — subdomain and service discovery

**Feasibility Assessment:**
- Can we build each one? Estimated complexity (MVP vs. full parity)?
- What would we do differently or better?
- What's the "table stakes" feature set users expect?
- What's our unfair advantage or differentiation angle?

---

## PHASE 3 — SECURITY ARCHITECTURE (Golden Standard from Day One)

Act as a security architect designing for GDPR + SOC 2 compliance. For OUR build:

**Data Protection:**
- Data classification — what's PII, what's sensitive, what's public?
- Encryption at rest and in transit — specific algorithms and key management
- Data retention policies — what do we keep, how long, how do we purge?
- Right to erasure — how do users delete their data completely?
- Data minimization — collect only what's needed

**Access Control & Auth:**
- Authentication architecture — MFA, session management, token rotation
- Authorization model — RBAC, ABAC, or attribute-based?
- API security — rate limiting, input validation, CORS, CSP headers
- Admin access — audit logs, principle of least privilege, break-glass procedures

**Infrastructure Hardening:**
- Environment isolation — dev/staging/prod separation
- Secret management — no hardcoded keys, vault integration
- Dependency scanning — automated CVE detection
- Container security if applicable
- Network security — firewall rules, VPC configuration

**Compliance Readiness:**
- GDPR checklist — consent management, DPA templates, breach notification procedures
- SOC 2 Type II — control mapping, evidence collection, audit trail
- Privacy policy and ToS requirements
- Cookie consent and tracking transparency

**Threat Modeling:**
- OWASP Top 10 mitigation for every endpoint
- Attack surface analysis
- Incident response plan outline
- Penetration testing recommendations

---

## PHASE 4 — PRE-LAUNCH GATE (Nothing Ships Without This)

Create a detailed pre-launch checklist report. When the user says "I'm ready to go live," run through EVERY item. Then run a SECOND independent verification pass.

**Checklist Categories:**
- Security audit — all findings resolved?
- Performance — load testing, response times, error rates benchmarked?
- Data backup & recovery — tested restore procedure?
- Monitoring & alerting — uptime, error tracking, log aggregation in place?
- Legal — privacy policy, ToS, cookie consent, GDPR compliance verified?
- DNS, SSL, domain configuration — all correct?
- Environment variables — all secrets properly configured in production?
- Database migrations — all applied, seed data verified?
- Third-party integrations — all API keys are PRODUCTION keys, not test?
- Rollback plan — can we revert in under 5 minutes?
- User-facing — onboarding flow tested, error messages helpful, empty states handled?
- Accessibility — basic a11y audit passed?
- SEO — meta tags, sitemap, robots.txt configured?
- Analytics — tracking in place for key metrics?

**The Double-Check Rule:**
After the first pass, run a second automated + manual verification. Flag any discrepancies. NOTHING launches until both passes are green.

---

## PHASE 5 — IP PROTECTION (The Secret Sauce Stays Secret)

Act as an IP protection specialist. For OUR build:

**Code-Level Protection:**
- Identify and catalog every piece of proprietary logic
- Core algorithms MUST be server-side only — never ship to client
- API responses reveal results, NEVER methodology
- Source maps disabled in production
- No comments, variable names, or error messages that reveal internal logic
- Obfuscate client-side code where appropriate

**Operational Security:**
- `.env` and secrets NEVER in version control
- Private repos with strict access controls
- Code review policy — no proprietary logic exposed in PRs to public repos
- Dependency audit — ensure we're not leaking through open-source contributions

**Legal Protection:**
- Licensing strategy for any public-facing code
- Proprietary notice in codebase
- NDA requirements for contributors

**Ongoing Vigilance:**
- Automated scanning for accidental exposure (GitHub secret scanning, etc.)
- Regular audit of what's public vs. private
- Monitor for clones/copies of our product
- Alert on any proprietary patterns appearing in public code

---

## DELIVERABLE FORMAT

Return a structured report with clear sections for each phase:
- Findings with evidence (links, data, sources)
- Recommendations with priority levels (CRITICAL / HIGH / MEDIUM / LOW)
- Action items with clear ownership
- Risk assessment where applicable
- Comparison matrix if multiple targets

**Always write the full report to `recon-reports/[target-name]/RECON_REPORT.md`** and print a condensed summary to chat.

---

## HOW TO INVOKE

**Full protocol (all 5 phases, deep):**
```
/recon https://competitor1.com https://competitor2.com
```

**Quick scan (phase 1 only, surface-level):**
```
/recon --quick https://competitor.com
```

**Specific phases:**
```
/recon --phases 1,2 https://competitor.com
```

**Build hardening only:**
```
/recon --phases 3,4,5
```

**Re-run with diff (compare to previous scan):**
```
/recon --diff https://competitor.com
```

**JSON output:**
```
/recon --format json https://competitor.com
```

**Combined flags:**
```
/recon --quick --format json https://competitor.com
/recon --deep --diff --phases 1,2 https://competitor.com
```

**Natural language (still works):**
> "I just saw [tool/website/product] — what is this? Full breakdown."
> Triggers phases 1-2 automatically

---

## EXECUTION STRATEGY

- Launch parallel research agents for each target (one agent per target per phase)
- Use WebFetch for website content extraction
- Use WebSearch for broader discovery
- Use Reddit MCP or WebSearch for community sentiment
- Use GitHub search for code discovery
- Cross-reference findings between targets for comparative analysis
- Write all findings to `recon-reports/` directory
- Print condensed summary to chat with file path references
- For `--diff` mode, load previous report and generate change analysis

### Research Source Priority Order

For each target, research agents should hit sources in this priority order:

**Tier 1 — Always check (core intelligence):**
- Target website (main, pricing, about, docs, blog, changelog)
- GitHub (org repos, open-source components, SDKs)
- Reddit (subreddit mentions, r/SaaS, r/startups, niche subreddits)
- Hacker News (launches, discussions, Show HN posts)
- Twitter/X (founder account, product account, mentions)
- LinkedIn (company page, founder profile, job postings)
- Crunchbase (funding, team, investors)

**Tier 2 — High-value secondary sources:**
- Product Hunt (launch history, upvotes, reviews)
- G2 / Capterra / Trustpilot (verified user reviews)
- Indie Hackers (founder stories, revenue milestones)
- Wayback Machine (site evolution, pivots, removed features)
- BuiltWith / Wappalyzer (tech stack detection)
- SimilarWeb (traffic, referrals, growth trends)
- GetLatka (SaaS revenue data)

**Tier 3 — Deep research (for --deep mode):**
- YouTube (conference talks, demos, founder interviews)
- Medium / Substack / dev.to (technical blog posts)
- Podcasts (founder interviews on Spotify, Apple Podcasts)
- SlideShare / Speaker Deck (architecture presentations)
- Glassdoor / Levels.fyi (culture, salaries, internal complaints)
- Wellfound / AngelList (startup profile, team, open roles)
- StackOverflow (developer adoption, API questions)
- Discord / Slack communities (real-time user feedback)
- npm / PyPI / Docker Hub (published packages)
- DNS / WHOIS / SSL transparency logs (infrastructure intel)
- Failory / Startup Obituary (post-mortems if applicable)
- Starter Story / Famewall (founder interviews, case studies)
- App Store / Play Store (if mobile — reviews, downloads, version history)
