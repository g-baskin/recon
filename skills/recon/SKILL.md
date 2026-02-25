---
name: recon
description: Run competitive intelligence reconnaissance on targets — tears through websites, APIs, tech stacks, communities, infrastructure, domains, IPs, emails, open ports, and stores findings in the project
user-invocable: true
argument-hint: "[--quick|--deep] [--phases 1,2,3] [--format md|json|csv] [--diff] [--compliance profile|bundle] <target URLs or product names>"
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

### Compliance Profiles

Select which compliance frameworks Phase 3 evaluates. Accepts individual profiles, preset bundles, or comma-separated combos.

**Individual profiles:**

| Flag | Framework | Scope |
|------|-----------|-------|
| `--compliance gdpr` | GDPR | EU data privacy |
| `--compliance ccpa` | CCPA/CPRA | California consumer privacy |
| `--compliance hipaa` | HIPAA + HITRUST | Healthcare / Protected Health Information |
| `--compliance pci` | PCI DSS | Payment card data security |
| `--compliance soc2` | SOC 2 Type II | B2B SaaS trust service criteria |
| `--compliance iso27001` | ISO 27001 | Enterprise information security management |
| `--compliance fedramp` | FedRAMP + NIST 800-53 | US federal government cloud |
| `--compliance nist` | NIST CSF | General cybersecurity risk framework |
| `--compliance cmmc` | CMMC | Defense contractor cybersecurity maturity |
| `--compliance sox` | SOX (Sarbanes-Oxley) | Financial reporting integrity |
| `--compliance glba` | GLBA | Consumer financial data protection |
| `--compliance owasp` | OWASP ASVS + Top 10 | Application security verification |
| `--compliance cis` | CIS Benchmarks | Infrastructure/OS/cloud hardening |
| `--compliance lgpd` | LGPD | Brazilian data protection |
| `--compliance pipeda` | PIPEDA | Canadian privacy |
| `--compliance popia` | POPIA | South African data protection |

**Preset bundles:**

| Bundle | Includes |
|--------|----------|
| `--compliance saas` | SOC 2 + GDPR + CCPA + OWASP |
| `--compliance healthcare` | HIPAA + HITRUST + SOC 2 + GDPR |
| `--compliance fintech` | PCI DSS + SOC 2 + GLBA + GDPR |
| `--compliance government` | FedRAMP + NIST 800-53 + CMMC |
| `--compliance startup` | OWASP Top 10 + GDPR + basic SOC 2 prep |
| `--compliance enterprise` | ISO 27001 + SOC 2 + GDPR + CCPA + CIS |
| `--compliance all` | Every framework |
| (no flag) | GDPR + SOC 2 + OWASP (default) |

**Custom combos:** `--compliance gdpr,hipaa,pci`

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

Act as a security architect. Evaluate the selected compliance profiles (from `--compliance` flag, default: GDPR + SOC 2 + OWASP). Run the **Shared Foundation** first, then each selected **Compliance Module**.

### Shared Foundation (Always Runs)

These apply regardless of which compliance profile is selected:

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

**Threat Modeling:**
- OWASP Top 10 mitigation for every endpoint
- Attack surface analysis
- Incident response plan outline
- Penetration testing recommendations

---

### Compliance Modules (Run Per Selected Profile)

For each selected compliance profile, run the corresponding module below. Each module adds framework-specific requirements **on top of** the shared foundation. Include for each: key requirements checklist, technical controls mapping, evidence/documentation needed, and common pitfalls.

---

#### Module: GDPR (General Data Protection Regulation)

**Who needs it:** Any application with EU/EEA users or processing EU citizen data.

**Key Requirements:**
- Lawful basis for processing — consent, legitimate interest, contract, legal obligation, vital interest, public task
- Consent management — granular, freely given, specific, informed, unambiguous; easy withdrawal
- Data Protection Impact Assessment (DPIA) — required for high-risk processing
- Data Processing Agreements (DPA) — required for all third-party processors
- Data residency — where is data stored? EU adequacy decisions for transfers (SCCs, binding corporate rules)
- Breach notification — 72-hour window to supervisory authority, "without undue delay" to affected individuals
- Right to erasure (Article 17) — complete deletion on request, cascading to all processors
- Right to portability (Article 20) — export user data in machine-readable format
- Data Protection Officer (DPO) — required for large-scale processing of sensitive data
- Cookie consent — opt-in required before non-essential cookies (ePrivacy Directive)
- Privacy by design and by default (Article 25)
- Records of processing activities (Article 30)

**Technical Controls:**
- Consent management platform or built-in consent tracking
- Data subject request (DSR) workflow — automated or documented manual process
- Data inventory and flow mapping
- Pseudonymization and anonymization capabilities
- Audit logging for all data access and modifications
- Automated data deletion/retention enforcement

**Evidence Needed:**
- Privacy policy (Articles 13/14 compliant)
- Cookie policy and consent mechanism
- DPA templates for processors
- DPIA documentation
- Records of processing activities
- Breach response procedure document

**Common Pitfalls:**
- Assuming "legitimate interest" covers everything — it doesn't, you need a balancing test
- Cookie walls (blocking access until consent) — non-compliant
- Not cascading deletion requests to third-party processors
- Storing data outside EU without adequate transfer mechanisms
- Pre-ticked consent checkboxes — explicitly non-compliant

---

#### Module: CCPA/CPRA (California Consumer Privacy Act / California Privacy Rights Act)

**Who needs it:** Apps with California users, >$25M annual revenue, or processing data of 100K+ consumers/households.

**Key Requirements:**
- Right to know — disclose what personal information is collected, used, shared, sold
- Right to delete — honor deletion requests within 45 days
- Right to opt-out of sale/sharing — prominent "Do Not Sell or Share My Personal Information" link
- Right to correct inaccurate personal information (CPRA addition)
- Right to limit use of sensitive personal information
- Non-discrimination — cannot deny service or charge more for exercising rights
- Privacy notice at or before collection — categories of PI, purpose, retention periods
- Service provider agreements — contractual restrictions on data use
- Annual cybersecurity audits for high-risk processors (CPRA addition)
- Risk assessments for processing that presents significant risk (CPRA addition)

**Technical Controls:**
- Opt-out mechanism (universal opt-out signal / GPC support)
- Data inventory — categories of PI collected, sources, purposes, third parties shared with
- Consumer request intake and fulfillment workflow (45-day response window)
- Identity verification for consumer requests
- Data deletion cascading to service providers
- "Sensitive personal information" flag and use-limitation controls

**Evidence Needed:**
- Privacy policy (updated annually, CCPA-specific disclosures)
- "Do Not Sell or Share" link on homepage
- Consumer request log and response records
- Service provider and contractor agreements
- Data inventory documentation

**Common Pitfalls:**
- Ignoring Global Privacy Control (GPC) browser signals — must be honored under CPRA
- Not distinguishing "sale" from "sharing" — CPRA covers both
- Missing the 12-month lookback requirement for disclosure requests
- Not updating privacy policy annually
- Treating all service providers identically — different rules for contractors vs. service providers

---

#### Module: HIPAA (Health Insurance Portability and Accountability Act)

**Who needs it:** Any application that creates, receives, maintains, or transmits Protected Health Information (PHI) — healthcare providers, health plans, clearinghouses, and their business associates.

**Key Requirements:**
- Privacy Rule — limits use and disclosure of PHI, minimum necessary standard
- Security Rule — administrative, physical, and technical safeguards for ePHI
- Breach Notification Rule — notify individuals within 60 days, HHS for breaches of 500+
- Business Associate Agreements (BAAs) — required for every vendor touching PHI
- Minimum necessary rule — access only the PHI needed for the task
- Patient rights — access, amendment, accounting of disclosures, restriction requests
- De-identification — Safe Harbor method (remove 18 identifiers) or Expert Determination
- Training — workforce must be trained on HIPAA policies

**Technical Controls (Security Rule):**
- Access controls — unique user IDs, emergency access procedures, automatic logoff, encryption
- Audit controls — record and examine activity in systems containing ePHI
- Integrity controls — mechanisms to authenticate ePHI, protect from alteration/destruction
- Transmission security — encryption for ePHI in transit (TLS 1.2+ minimum)
- Encryption at rest — AES-256 for stored ePHI
- Facility access controls — physical safeguards for data centers
- Device and media controls — disposal, re-use, accountability, data backup
- Workstation security — policies for workstations accessing ePHI
- Authentication — verify identity of persons seeking access to ePHI

**HITRUST CSF Overlay:**
- Maps HIPAA requirements to specific controls
- 14 control categories, 49 control objectives, 156 control specifications
- Certification levels: e1 (essential), i1 (implemented), r2 (risk-based)
- Recommended for enterprise healthcare SaaS seeking third-party validation

**Evidence Needed:**
- Risk analysis and risk management plan
- BAAs with all business associates
- Policies and procedures documentation
- Workforce training records
- Incident response and breach notification procedures
- System activity logs and audit reports

**Common Pitfalls:**
- Assuming cloud provider BAA covers your application — it covers infrastructure, not your app logic
- Logging PHI in application logs or error tracking (Sentry, Datadog)
- Not encrypting ePHI at rest — "addressable" does not mean "optional"
- Sharing PHI in development/staging environments without de-identification
- Missing BAAs with analytics, email, or support tool vendors that incidentally access PHI

---

#### Module: PCI DSS (Payment Card Industry Data Security Standard)

**Who needs it:** Any application that processes, stores, or transmits credit card data. Applies to merchants and service providers.

**Key Requirements (12 Requirements):**
1. Install and maintain network security controls (firewalls, network segmentation)
2. Apply secure configurations to all system components
3. Protect stored account data — encryption, masking, truncation, hashing
4. Protect cardholder data with strong cryptography during transmission
5. Protect all systems and networks from malicious software
6. Develop and maintain secure systems and software
7. Restrict access to system components and cardholder data by business need-to-know
8. Identify users and authenticate access to system components
9. Restrict physical access to cardholder data
10. Log and monitor all access to system components and cardholder data
11. Test security of systems and networks regularly (vulnerability scans, pen tests)
12. Support information security with organizational policies and programs

**Technical Controls:**
- Network segmentation — isolate cardholder data environment (CDE) from other systems
- Tokenization — replace card data with non-reversible tokens (use Stripe/payment processor tokens)
- Point-to-point encryption (P2PE) — encrypt card data at point of interaction
- Web Application Firewall (WAF) — required for public-facing web applications
- File integrity monitoring (FIM) — detect unauthorized changes to critical files
- Intrusion detection/prevention (IDS/IPS)
- Quarterly ASV (Approved Scanning Vendor) vulnerability scans
- Annual penetration testing
- Secure key management — key rotation, split knowledge, dual control

**Compliance Levels:**
- Level 1: >6M transactions/year — annual on-site QSA assessment + quarterly ASV scans
- Level 2: 1-6M transactions/year — annual SAQ + quarterly ASV scans
- Level 3: 20K-1M e-commerce transactions/year — annual SAQ + quarterly ASV scans
- Level 4: <20K e-commerce or <1M other — annual SAQ + quarterly ASV scans

**Evidence Needed:**
- Self-Assessment Questionnaire (SAQ) or Report on Compliance (ROC)
- Quarterly ASV scan reports
- Penetration test reports
- Network diagrams showing CDE segmentation
- Policies and procedures for all 12 requirements

**Common Pitfalls:**
- Storing full card numbers when a tokenized solution (Stripe, Braintree) eliminates PCI scope
- Not segmenting the CDE — everything in the flat network becomes in-scope
- Missing SAQ type — different questionnaires for different integration methods
- Logging card data in application logs
- Using Stripe but still handling raw card data on your server (use Stripe.js/Elements for client-side tokenization)
- **Best practice:** Use Stripe Checkout or Elements to avoid card data ever touching your servers — reduces to SAQ A (simplest)

---

#### Module: SOC 2 Type II (Service Organization Control 2)

**Who needs it:** B2B SaaS companies, cloud service providers, any organization that processes customer data and needs to demonstrate trustworthiness to enterprise buyers.

**Trust Service Criteria (pick applicable ones):**
- **Security** (Common Criteria — required) — protection against unauthorized access
- **Availability** — system is available for operation and use as committed
- **Processing Integrity** — system processing is complete, valid, accurate, timely
- **Confidentiality** — information designated as confidential is protected
- **Privacy** — personal information is collected, used, retained, disclosed per notice

**Key Requirements:**
- Define control objectives for each selected trust service criteria
- Implement controls that meet those objectives
- Maintain evidence that controls operate effectively over time (Type II = 3-12 month observation period)
- Annual audit by a CPA firm (AICPA licensed)
- Management assertion letter
- Continuous monitoring between audits

**Technical Controls:**
- Change management — documented approval process for all production changes
- Logical access controls — RBAC, MFA, access reviews (quarterly), onboarding/offboarding
- Incident management — documented response procedures, post-incident reviews
- Vulnerability management — regular scanning, patching cadence, risk-based prioritization
- Monitoring and alerting — centralized logging, anomaly detection, uptime monitoring
- Backup and recovery — documented procedures, tested restore, RTO/RPO defined
- Vendor management — third-party risk assessments, vendor inventory
- Encryption — at rest and in transit
- Network security — firewalls, IDS, network segmentation
- Physical security — data center access controls (typically handled by cloud provider)

**Evidence Needed:**
- Policy documents (security, acceptable use, incident response, change management, etc.)
- Access review logs (quarterly)
- Change management tickets and approvals
- Vulnerability scan reports
- Incident response logs
- Backup and restore test records
- Vendor risk assessment records
- Employee onboarding/offboarding checklists
- Security awareness training records

**Common Pitfalls:**
- Starting the audit without 3+ months of evidence — Type II requires observation period
- Overscoping — include only systems that process customer data
- Treating it as a one-time project — SOC 2 requires continuous compliance
- Missing vendor risk assessments for critical third-party services
- No formal change management process (even for small teams)
- Not having a formal incident response plan (even if you've never had an incident)

---

#### Module: ISO 27001 (Information Security Management System)

**Who needs it:** Organizations targeting international enterprise customers, government contracts, or regulated industries outside the US. Recognized globally.

**Key Requirements:**
- Establish an ISMS (Information Security Management System) — scope, context, leadership commitment
- Risk assessment methodology — identify, analyze, evaluate information security risks
- Risk treatment plan — select controls from Annex A or other sources
- Statement of Applicability (SoA) — document which Annex A controls apply and why
- Internal audit program — regular audits of the ISMS
- Management review — leadership reviews ISMS performance
- Continual improvement — corrective actions, lessons learned

**Annex A Controls (93 controls in ISO 27001:2022, grouped into 4 themes):**
- **Organizational controls (37)** — policies, roles, asset management, access control, supplier relationships, incident management, compliance
- **People controls (8)** — screening, terms of employment, awareness, disciplinary process, post-employment
- **Physical controls (14)** — perimeters, entry controls, equipment protection, secure disposal, clear desk
- **Technological controls (34)** — endpoint devices, privileged access, information access restriction, secure authentication, capacity management, malware protection, backups, logging, network security, secure development, testing

**Technical Controls:**
- Asset inventory — all information assets classified and owned
- Access control policy — need-to-know, least privilege, regular reviews
- Cryptography — encryption policy, key management
- Operations security — change management, capacity management, malware protection
- Communications security — network controls, segregation, transfer policies
- System acquisition/development — secure development lifecycle, test data, outsourced development
- Supplier relationships — information security in supplier agreements, monitoring

**Certification Process:**
- Stage 1 audit — documentation review (ISMS design)
- Stage 2 audit — implementation and effectiveness review (on-site/remote)
- 3-year certification cycle with annual surveillance audits
- Accredited certification body (UKAS, ANAB, etc.)

**Evidence Needed:**
- ISMS manual and scope document
- Risk assessment and treatment records
- Statement of Applicability
- Policies for all applicable Annex A controls
- Internal audit reports
- Management review minutes
- Corrective action records
- Training and awareness records

**Common Pitfalls:**
- Writing policies that don't match actual practices
- Overscoping the ISMS — start with a defined scope and expand
- Treating it as an IT project — ISO 27001 is a management system, requires leadership involvement
- Not maintaining the ISMS between surveillance audits
- Missing the risk-based approach — controls must be justified by risk assessment, not just checkboxed

---

#### Module: FedRAMP (Federal Risk and Authorization Management Program)

**Who needs it:** Cloud service providers selling to US federal government agencies.

**Key Requirements:**
- Based on NIST 800-53 security controls (Rev. 5)
- Three impact levels with increasing control baselines:
  - **Low** — ~156 controls (low-impact data, public websites)
  - **Moderate** — ~325 controls (most federal workloads, CUI)
  - **High** — ~421 controls (law enforcement, emergency services, financial, health)
- Authorization process — requires a federal agency sponsor or Joint Authorization Board (JAB)
- Third-Party Assessment Organization (3PAO) — independent assessor conducts initial and annual assessments
- Plan of Action and Milestones (POA&M) — document and track remediation of findings
- Continuous monitoring — monthly vulnerability scans, annual pen tests, annual assessment

**NIST 800-53 Control Families (20 families):**
- AC (Access Control), AT (Awareness & Training), AU (Audit & Accountability)
- CA (Assessment & Authorization), CM (Configuration Management), CP (Contingency Planning)
- IA (Identification & Authentication), IR (Incident Response), MA (Maintenance)
- MP (Media Protection), PE (Physical & Environmental), PL (Planning)
- PM (Program Management), PS (Personnel Security), PT (PII Processing & Transparency)
- RA (Risk Assessment), SA (System & Services Acquisition), SC (System & Communications Protection)
- SI (System & Information Integrity), SR (Supply Chain Risk Management)

**Technical Controls (highlights):**
- FIPS 140-2/140-3 validated cryptographic modules
- PIV/CAC authentication support for federal users
- Continuous monitoring — automated vulnerability scanning, SIEM, log aggregation
- Incident response — report to US-CERT within timeframes based on severity
- Boundary protection — DMZ, WAF, IDS/IPS at authorization boundary
- Configuration management — hardened baselines (CIS, DISA STIGs)
- Supply chain risk management — vendor vetting, software composition analysis

**Authorization Package:**
- System Security Plan (SSP)
- Security Assessment Plan (SAP)
- Security Assessment Report (SAR)
- Plan of Action & Milestones (POA&M)
- Continuous monitoring deliverables (ConMon)

**Common Pitfalls:**
- Underestimating the timeline — 12-18 months for initial authorization
- Not having a federal agency sponsor lined up
- Insufficient documentation — FedRAMP is extremely documentation-heavy
- Using non-FIPS validated cryptography
- Not planning for continuous monitoring burden post-authorization

---

#### Module: NIST CSF (Cybersecurity Framework)

**Who needs it:** Any organization wanting a structured, risk-based approach to cybersecurity. Voluntary framework, widely adopted as a best-practice baseline.

**Five Core Functions:**
1. **Identify** — asset management, business environment, governance, risk assessment, risk management strategy, supply chain risk management
2. **Protect** — identity management & access control, awareness & training, data security, information protection processes, maintenance, protective technology
3. **Detect** — anomalies & events, security continuous monitoring, detection processes
4. **Respond** — response planning, communications, analysis, mitigation, improvements
5. **Recover** — recovery planning, improvements, communications

**Implementation Tiers:**
- Tier 1 (Partial) — ad hoc, reactive risk management
- Tier 2 (Risk Informed) — risk management practices approved but not organization-wide
- Tier 3 (Repeatable) — formally approved, regularly updated risk management
- Tier 4 (Adaptive) — organization adapts based on lessons learned and predictive indicators

**Technical Controls (per function):**
- Identify — asset inventory, data classification, third-party catalog, risk register
- Protect — MFA, encryption, secure SDLC, backup procedures, access control policies
- Detect — SIEM, IDS/IPS, anomaly detection, log monitoring, threat intelligence feeds
- Respond — incident response plan, communication templates, forensics capability, containment procedures
- Recover — disaster recovery plan, backup restoration testing, post-incident review, communication plan

**Evidence Needed:**
- Current Profile (where you are) and Target Profile (where you want to be)
- Gap analysis between current and target
- Risk register
- Implementation roadmap with priorities

**Common Pitfalls:**
- Treating it as a compliance checkbox — it's a risk management framework, not a checklist
- Skipping the "Identify" function — you can't protect what you don't know about
- Not establishing a Target Profile — without a target, there's no gap to close
- Ignoring the "Recover" function — incident response without recovery planning is incomplete

---

#### Module: CMMC (Cybersecurity Maturity Model Certification)

**Who needs it:** Defense contractors and subcontractors handling Controlled Unclassified Information (CUI) or Federal Contract Information (FCI) for the US Department of Defense.

**Maturity Levels (CMMC 2.0):**
- **Level 1 (Foundational)** — 17 practices from FAR 52.204-21, annual self-assessment, protects FCI
- **Level 2 (Advanced)** — 110 practices from NIST SP 800-171, triennial third-party assessment (C3PAO), protects CUI
- **Level 3 (Expert)** — 110+ practices from NIST SP 800-172 (enhanced), government-led assessment, protects CUI against APTs

**Key Requirements:**
- Identify all CUI in your environment — data flows, storage, processing, transmission
- Implement required practices for your level
- Document implementation in System Security Plan (SSP)
- Create POA&M for any gaps (limited POA&M allowed under CMMC 2.0)
- Assessment by C3PAO (Level 2) or DIBCAC (Level 3)

**Technical Controls (Level 2 highlights — NIST 800-171):**
- Access control — limit system access to authorized users, limit access to types of transactions
- Audit and accountability — create, protect, retain system audit logs
- Configuration management — establish and enforce security configuration settings
- Identification and authentication — identify and authenticate users, devices, processes
- Incident response — establish operational incident-handling capability
- Media protection — sanitize or destroy media containing CUI before disposal
- System and communications protection — monitor, control, and protect communications at boundaries
- System and information integrity — identify, report, and correct system flaws in a timely manner

**Evidence Needed:**
- System Security Plan (SSP)
- Plan of Action & Milestones (POA&M)
- Network diagrams showing CUI boundaries
- Policy and procedure documents for all 110 practices (Level 2)
- Evidence of practice implementation (screenshots, configs, logs)

**Common Pitfalls:**
- Underestimating CUI scope — CUI can exist in email, file shares, cloud storage, backups
- Assuming commercial cloud = compliant — cloud services must meet FedRAMP Moderate equivalent
- Not having an SSP before assessment — it's the foundation document
- POA&M as a crutch — CMMC 2.0 limits reliance on POA&Ms, controls must be implemented

---

#### Module: SOX (Sarbanes-Oxley Act)

**Who needs it:** Publicly traded companies (US) and their IT service providers. Focuses on financial reporting integrity.

**Key Requirements:**
- Section 302 — CEO/CFO certify accuracy of financial reports
- Section 404 — management assesses effectiveness of internal controls over financial reporting (ICFR)
- IT General Controls (ITGCs) — controls over IT systems that support financial reporting
- Segregation of duties — no single person controls end-to-end financial transactions
- Audit trail — complete traceability of all financial transactions and changes

**IT General Controls (ITGCs):**
- Access to programs and data — logical access controls, user provisioning/deprovisioning, periodic access reviews
- Program changes — change management with approval workflows, testing, separation of dev/prod
- Program development — secure SDLC for financially significant applications
- Computer operations — job scheduling, backup/recovery, incident management, capacity planning

**Technical Controls:**
- Role-based access control with segregation of duties enforcement
- Change management system with approval workflow and audit trail
- Automated user access reviews (quarterly for privileged, semi-annual for standard)
- Database activity monitoring for financially significant systems
- Backup and recovery with documented and tested procedures
- Audit logging — immutable, centralized, retained per policy (typically 7 years)

**Evidence Needed:**
- ITGC control matrices
- Access review documentation
- Change management records (tickets, approvals, testing evidence)
- Backup and recovery test results
- Segregation of duties matrix
- Annual management assessment of ICFR effectiveness

**Common Pitfalls:**
- Focusing only on financial applications — ITGCs apply to all supporting infrastructure (AD, databases, OS)
- Informal change management — even small teams need documented approval processes
- Not reviewing service organization reports (SOC 1) for critical SaaS vendors
- Treating SOX as an annual event — controls must operate continuously

---

#### Module: GLBA (Gramm-Leach-Bliley Act)

**Who needs it:** Financial institutions — banks, credit unions, insurance companies, securities firms, fintech apps, and companies offering financial products or services to consumers.

**Key Requirements:**
- **Safeguards Rule** — develop, implement, and maintain a comprehensive information security program
- **Privacy Rule** — provide privacy notices explaining information-sharing practices
- **Pretexting Protection** — protect against unauthorized access to personal information through social engineering
- Designate a qualified individual to oversee the security program
- Risk assessment — identify reasonably foreseeable risks to customer information
- Vendor management — oversee service providers handling customer data

**Safeguards Rule Technical Requirements (updated 2023):**
- Designate a qualified individual to oversee information security program
- Conduct periodic risk assessments
- Implement safeguards to control identified risks:
  - Access controls — limit who can access customer information
  - Encryption — encrypt customer information in transit and at rest
  - MFA — for any individual accessing customer information
  - Secure disposal of customer information within 2 years of last use
  - Change management — procedures for evaluating and adjusting the security program
  - Activity monitoring — logging access to customer information systems
- Incident response plan
- Annual reporting to board of directors on security program status
- Continuous monitoring and testing of safeguards

**Evidence Needed:**
- Written information security program
- Risk assessment documentation
- Privacy notices (initial and annual)
- Service provider agreements with security requirements
- Incident response plan
- Board-level security reports
- Employee training records

**Common Pitfalls:**
- Assuming GLBA only applies to banks — any company significantly engaged in financial activities is covered
- Not updating the security program after the 2023 Safeguards Rule changes
- Missing the MFA requirement — now mandatory under updated Safeguards Rule
- Not including non-traditional financial services (debt collection, tax preparation, real estate settlement)

---

#### Module: OWASP ASVS (Application Security Verification Standard)

**Who needs it:** Any web application. OWASP ASVS provides a comprehensive checklist for application security testing and secure development. Complements other compliance frameworks by providing the technical "how."

**Verification Levels:**
- **Level 1 (Opportunistic)** — automated testing + basic manual review. Minimum for all applications.
- **Level 2 (Standard)** — defense in depth. Recommended for applications handling sensitive data.
- **Level 3 (Advanced)** — comprehensive verification. For critical applications (healthcare, banking, infrastructure).

**Chapter Requirements:**

| Chapter | Focus Area | Key Controls |
|---------|-----------|--------------|
| V1 | Architecture & Design | Threat modeling, secure SDLC, dependency management |
| V2 | Authentication | Credential storage (bcrypt/argon2), MFA, session binding, anti-automation |
| V3 | Session Management | Session tokens, timeouts, concurrent session limits, binding to IP/device |
| V4 | Access Control | RBAC/ABAC enforcement, principle of least privilege, server-side enforcement |
| V5 | Input Validation | Allow-list validation, output encoding, parameterized queries, file upload restrictions |
| V6 | Cryptography | Approved algorithms only, key management, random number generation |
| V7 | Error Handling & Logging | No sensitive data in errors/logs, centralized logging, tamper-evident logs |
| V8 | Data Protection | Sensitive data classification, PII handling, data-at-rest encryption, HTTP security headers |
| V9 | Communications | TLS everywhere, certificate pinning (mobile), HSTS, no mixed content |
| V10 | Malicious Code | Integrity verification, no backdoors, dependency scanning, SRI for CDN resources |
| V11 | Business Logic | Rate limiting, anti-automation, workflow validation, abuse case testing |
| V12 | Files & Resources | File upload validation, path traversal prevention, SSRF protection |
| V13 | API Security | RESTful security, GraphQL depth limiting, input validation, rate limiting |
| V14 | Configuration | Secure defaults, HTTP security headers (CSP, X-Frame-Options, etc.), dependency management |

**Technical Controls (highlights):**
- Credential storage — bcrypt (cost 10+), scrypt, or Argon2id only
- Session management — cryptographically random tokens, secure cookie attributes, absolute timeout
- Input validation — server-side allow-list validation for all user input
- Output encoding — context-appropriate encoding (HTML, JS, URL, CSS, SQL)
- Parameterized queries — no string concatenation for SQL/LDAP/XPath
- HTTP security headers — CSP, HSTS, X-Content-Type-Options, X-Frame-Options, Referrer-Policy
- Dependency management — SCA (Software Composition Analysis), automated CVE alerts
- API security — authentication on every endpoint, rate limiting, schema validation

**Evidence Needed:**
- ASVS self-assessment or third-party assessment report
- Automated SAST/DAST scan results
- Penetration test report mapping to ASVS chapters
- Secure coding guidelines document
- Dependency scan reports

**Common Pitfalls:**
- Only doing automated scanning — ASVS Level 2+ requires manual review
- Validating input on the client side only — server-side validation is mandatory
- Using MD5/SHA1 for password hashing — use bcrypt, scrypt, or Argon2id
- Missing HTTP security headers — easy wins that are often overlooked
- Not testing business logic — automated scanners can't find logic flaws

---

#### Module: CIS Benchmarks (Center for Internet Security)

**Who needs it:** Any organization that needs hardened configurations for operating systems, cloud platforms, containers, databases, and network devices. Complements higher-level frameworks (SOC 2, ISO 27001) with actionable configuration guidance.

**Benchmark Categories:**

| Category | Examples | Key Focus |
|----------|----------|-----------|
| **OS Hardening** | Ubuntu, RHEL, Windows Server, macOS | File permissions, service hardening, audit logging, kernel parameters |
| **Cloud Platforms** | AWS, Azure, GCP | IAM, network security groups, logging, encryption defaults, storage policies |
| **Containers** | Docker, Kubernetes | Image security, runtime restrictions, network policies, RBAC, secrets management |
| **Databases** | PostgreSQL, MySQL, MongoDB, Redis | Authentication, encryption, audit logging, network exposure, default credentials |
| **Web Servers** | Nginx, Apache | TLS config, header security, access controls, logging, module hardening |
| **Network Devices** | Firewalls, routers, switches | Default credential removal, access lists, logging, firmware updates |

**Implementation Levels:**
- **Level 1** — basic hardening, minimal performance impact, broad applicability
- **Level 2** — defense in depth, may impact performance or functionality, for high-security environments

**Technical Controls (cloud platform highlights):**
- AWS — enable CloudTrail in all regions, restrict root account usage, enable MFA on root, encrypt S3 buckets by default, restrict security group ingress, enable VPC flow logs, enable GuardDuty
- Azure — enable Security Center, enable diagnostic logging, restrict network security group rules, encrypt storage accounts, enable Key Vault, enable Azure AD MFA
- GCP — enable audit logging, restrict default service account, enable VPC flow logs, encrypt Cloud Storage by default, restrict firewall rules, enable Security Command Center

**Evidence Needed:**
- CIS-CAT scan results (automated benchmark assessment)
- Configuration audit reports
- Hardening baseline documentation
- Exception documentation for any non-compliant items

**Common Pitfalls:**
- Applying benchmarks without testing — some controls break applications
- Only hardening OS and ignoring cloud platform configuration
- Not re-scanning after updates/patches — drift happens
- Treating CIS as one-time — ongoing configuration management needed

---

#### Module: LGPD (Lei Geral de Proteção de Dados — Brazilian General Data Protection Law)

**Who needs it:** Any application processing personal data of individuals in Brazil, regardless of where the organization is based.

**Key Requirements:**
- Legal basis for processing — 10 legal bases (consent, legitimate interest, contract performance, regulatory compliance, research, credit protection, etc.)
- Consent — specific, informed, freely given; separate from other consents; easy withdrawal
- Data Protection Officer (DPO/Encarregado) — required for all controllers
- Data Processing Impact Report (RIPD) — equivalent to GDPR's DPIA
- International data transfers — only to countries with adequate protection level or with contractual safeguards
- Data subject rights — confirmation, access, correction, anonymization, portability, deletion, information about sharing, consent revocation
- Breach notification — communicate to ANPD (National Authority) and data subjects within "reasonable time"
- Records of processing activities
- Privacy by design and by default

**Key Differences from GDPR:**
- 10 legal bases (vs. GDPR's 6) — includes credit protection and health protection
- DPO required for ALL controllers (GDPR only for certain cases)
- No specific breach notification timeline (GDPR has 72 hours)
- ANPD can request a RIPD at any time (not just for high-risk processing)

**Evidence Needed:**
- Privacy policy in Portuguese
- Consent records
- DPO appointment documentation
- RIPD (Data Protection Impact Report)
- Records of processing activities
- International transfer mechanisms

---

#### Module: PIPEDA (Personal Information Protection and Electronic Documents Act — Canada)

**Who needs it:** Organizations collecting, using, or disclosing personal information in the course of commercial activities in Canada.

**10 Fair Information Principles:**
1. **Accountability** — organization is responsible for personal information under its control; designate a privacy officer
2. **Identifying Purposes** — document and communicate purposes for collection at or before time of collection
3. **Consent** — knowledge and consent required for collection, use, disclosure (express or implied depending on sensitivity)
4. **Limiting Collection** — collect only what's necessary for identified purposes
5. **Limiting Use, Disclosure, and Retention** — use only for stated purposes; retain only as long as necessary
6. **Accuracy** — keep personal information accurate, complete, up-to-date
7. **Safeguards** — protect with appropriate security (physical, organizational, technological)
8. **Openness** — make policies and practices readily available
9. **Individual Access** — right to access and challenge accuracy of their information
10. **Challenging Compliance** — individuals can challenge compliance; organization must have complaint procedures

**Technical Controls:**
- Consent tracking and management
- Purpose limitation enforcement
- Data retention automation
- Access request fulfillment workflow
- Breach notification to OPC (Office of the Privacy Commissioner) and affected individuals
- Safeguards proportional to sensitivity of information

**Key Differences from GDPR:**
- Consent can be implied for non-sensitive information (GDPR requires explicit for sensitive)
- No mandatory DPO (but must designate someone accountable)
- Breach notification required when "real risk of significant harm"
- Provincial privacy laws may apply instead (Alberta, BC, Quebec)

**Evidence Needed:**
- Privacy policy
- Consent records
- Breach reporting records
- Privacy officer designation
- Complaint handling procedures

---

#### Module: POPIA (Protection of Personal Information Act — South Africa)

**Who needs it:** Any organization processing personal information of South African data subjects.

**Key Requirements:**
- Register with the Information Regulator
- Appoint an Information Officer
- 8 conditions for lawful processing:
  1. Accountability
  2. Processing limitation
  3. Purpose specification
  4. Further processing limitation
  5. Information quality
  6. Openness
  7. Security safeguards
  8. Data subject participation
- Consent — voluntary, specific, informed
- Data subject rights — access, correction, deletion, objection to processing
- Breach notification — notify Information Regulator and data subjects "as soon as reasonably possible"
- Cross-border transfer restrictions — adequate protection or consent or contractual safeguards
- Special personal information — stricter rules for race, health, biometrics, religion, etc.

**Key Differences from GDPR:**
- Must register with Information Regulator
- Information Officer required (vs. DPO only in certain GDPR cases)
- Includes juristic persons (companies), not just natural persons
- No specific breach notification timeline
- Direct marketing requires express opt-in consent

**Evidence Needed:**
- Information Regulator registration
- Information Officer appointment
- PAIA (Promotion of Access to Information Act) manual
- Privacy policy
- Consent records
- Security safeguards documentation
- Breach notification procedures

---

### Compliance Module Output Format

For each selected compliance module, the Phase 3 report section should include:

```markdown
### [Framework Name] Compliance Assessment

**Applicability:** [Why this framework applies to this build]
**Current Posture:** [Gap assessment — what's in place vs. what's missing]

#### Requirements Checklist
- [ ] [Requirement 1] — [Status: Met / Partial / Not Met] — [Notes]
- [ ] [Requirement 2] — [Status] — [Notes]
...

#### Technical Controls Needed
| Control | Priority | Implementation Notes |
|---------|----------|---------------------|
| [Control] | CRITICAL/HIGH/MEDIUM/LOW | [How to implement] |

#### Evidence & Documentation Gaps
- [Document needed] — [Status: Exists / Draft / Missing]

#### Common Pitfalls to Avoid
- [Pitfall] — [Mitigation]

#### Estimated Effort
- [Quick wins — can be done in days]
- [Medium-term — weeks of work]
- [Long-term — months or ongoing program]
```

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

**Compliance-focused scan (specific frameworks):**
```
/recon --phases 3 --compliance hipaa
/recon --phases 3 --compliance gdpr,hipaa,pci
```

**Compliance bundle (preset profiles):**
```
/recon --compliance healthcare
/recon --compliance fintech
/recon --compliance saas https://competitor.com
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
