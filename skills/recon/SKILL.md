---
name: recon
description: Run competitive intelligence reconnaissance on targets — tears through websites, APIs, tech stacks, communities, infrastructure, domains, IPs, emails, open ports, and stores findings in the project
user-invocable: true
argument-hint: <target URLs or product names>
allowed-tools: Bash, Read, Write, Glob, Grep, WebFetch, WebSearch, Task
---

# Recon — Deep Competitive Intelligence & Build Protocol

> **Author:** Greg Baskin ([github.com/g-baskin](https://github.com/g-baskin))
> **License:** MIT

When the user mentions websites, tools, software, products, or competitors they want investigated — or says anything like "run the protocol," "research this," "tear this apart," "investigate these," "act as a hacker," or names specific URLs/products to analyze — execute this full 5-phase pipeline. **Do NOT summarize early. Go deep on every phase before moving to the next.**

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

**Research Tools to Use (in parallel):**
- WebSearch for each target name + variations
- WebFetch on their main site, about page, pricing page, docs, blog
- WebFetch on Wayback Machine snapshots: `https://web.archive.org/web/*/targeturl.com`
- WebSearch for "[target] review," "[target] alternative," "[target] funding"
- Reddit search for mentions in relevant subreddits
- GitHub search for their org name
- Crunchbase / PitchBook via web search

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
- Developer blog posts explaining architecture decisions
- Conference talks by their engineers (search YouTube, SlideShare)
- Technical write-ups, postmortems, architecture diagrams
- Dependencies in any public package files (package.json, requirements.txt, etc.)
- BuiltWith, Wappalyzer, or similar for tech stack fingerprinting
- DNS records, SSL certificate info, WHOIS data

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

---

## HOW TO INVOKE

**Full protocol (all 5 phases):**
> `/recon https://competitor1.com https://competitor2.com`

**Research only (phases 1-2):**
> "Run phases 1-2 on [product name]"

**Build hardening (phases 3-5):**
> "Run phases 3-5 on our implementation"

**Single phase:**
> "Run phase 4" (pre-launch gate only)
> "Check our secret sauce" (phase 5 only)

**Ad-hoc research:**
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
- Compile all findings into a single structured report before presenting
