# Recon — Competitive Intelligence Skill for Claude Code

A Claude Code skill that runs deep competitive intelligence reconnaissance on any target — websites, products, tools, or competitors. It tears through business intel, tech stacks, APIs, communities, infrastructure, security posture, and more.

## What It Does

Recon runs a 5-phase pipeline on any target you give it:

| Phase | Name | What It Covers |
|-------|------|----------------|
| 1 | **Full Reconnaissance** | Business intel, web presence, funding, team, competitive landscape |
| 2 | **Technical Reverse Engineering** | Tech stack, APIs, architecture, open source, OSINT, feasibility |
| 3 | **Security Architecture** | Data protection, auth, infrastructure hardening, compliance, threat modeling |
| 4 | **Pre-Launch Gate** | Full checklist before shipping — security, performance, legal, rollback |
| 5 | **IP Protection** | Protect your proprietary logic, operational security, legal protection |

## Installation

Copy the skill folder into your Claude Code skills directory:

```bash
# Clone this repo
git clone https://github.com/g-baskin/recon.git

# Copy the skill to your Claude Code skills directory
cp -r recon/skills/recon ~/.claude/skills/recon
```

After copying, restart Claude Code. The `/recon` command will be available in all your projects.

### Alternative: Project-Level Install

To add recon to a specific project only (shared with your team via git):

```bash
# From your project root
mkdir -p .claude/skills
cp -r /path/to/recon/skills/recon .claude/skills/recon
```

## Usage

### Full protocol (all 5 phases)
```
/recon https://competitor1.com https://competitor2.com
```

### Research only (phases 1-2)
```
Run phases 1-2 on [product name]
```

### Build hardening (phases 3-5)
```
Run phases 3-5 on our implementation
```

### Single phase
```
Run phase 4
```

### Ad-hoc research
```
I just saw [tool/website] — what is this? Full breakdown.
```

## How It Works

Recon uses Claude Code's parallel agent system to maximize throughput:

- Launches multiple research agents simultaneously (one per target per phase)
- Uses WebSearch for broad discovery
- Uses WebFetch for deep website content extraction
- Searches Reddit, GitHub, Hacker News for community sentiment
- Cross-references findings across targets for comparative analysis
- Delivers a structured report with evidence, recommendations, and priority levels

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed and configured
- WebSearch and WebFetch tools available (included with Claude Code)
- Optional: Reddit MCP server for deeper community research

## License

GPL-3.0 License - Copyright (c) 2026 AutomationGod

See [LICENSE](LICENSE) for details.

If you modify or redistribute this skill, you must:
- Keep the original copyright and credit
- Share your modifications under GPL-3.0
