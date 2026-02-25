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

## Features

- **Configurable depth** — `--quick` for surface-level scans, `--deep` (default) for the full pipeline
- **Phase selection** — run only the phases you need with `--phases 1,2`
- **File output** — full reports saved to `recon-reports/` directory, not just printed to chat
- **Multiple export formats** — Markdown (default), JSON, or CSV
- **Diff mode** — re-run on the same target and see what changed since last scan
- **Auto comparison matrix** — side-by-side table when scanning 2+ targets
- **Target type detection** — auto-detects SaaS, open source, API, mobile app, CLI tool, etc. and adjusts research accordingly
- **Parallel execution** — launches multiple research agents simultaneously for speed

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

### Full scan (default — all 5 phases, deep)
```
/recon https://competitor1.com https://competitor2.com
```

### Quick scan (phase 1 only, surface-level)
```
/recon --quick https://competitor.com
```

### Specific phases only
```
/recon --phases 1,2 https://competitor.com
```

### Build hardening (phases 3-5, for your own project)
```
/recon --phases 3,4,5
```

### Re-run with diff (compare to previous scan)
```
/recon --diff https://competitor.com
```

### JSON or CSV output
```
/recon --format json https://competitor.com
/recon --format csv https://competitor.com
```

### Combined flags
```
/recon --quick --format json https://competitor.com
/recon --deep --diff --phases 1,2 https://competitor.com
```

### Natural language (also works)
```
I just saw [tool/website/product] — what is this? Full breakdown.
```

## Output

Reports are saved to your project directory:

```
recon-reports/
├── competitor-name/
│   ├── RECON_REPORT.md          # Full report
│   ├── RECON_REPORT.json        # Structured data (if --format json)
│   ├── comparison.csv           # Tables (if --format csv)
│   ├── metadata.json            # Run metadata (date, flags, target type)
│   ├── DIFF_REPORT.md           # Changes since last scan (if --diff)
│   └── previous/               # Previous reports for diffing
└── COMPARISON_MATRIX.md         # Cross-target comparison (multiple targets)
```

A condensed summary is also printed to chat with pointers to the full report files.

## Flags Reference

| Flag | Description | Default |
|------|-------------|---------|
| `--quick` | Surface-level scan (phase 1 only) | Off |
| `--deep` | Full 5-phase pipeline | On |
| `--phases 1,2,3` | Run specific phases | All |
| `--format md\|json\|csv` | Output format | `md` |
| `--diff` | Compare against previous scan | Off |

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
