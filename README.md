# Claude Code Skills Library

A collection of custom skills for [Claude Code](https://claude.ai/code) — specialized agents that work standalone or chain together as orchestrated pipelines to validate, plan, architect, and launch tech products.

Built and maintained by [@RashiD2801](https://github.com/RashiD2801). Calibrated for India-first builders, but useful anywhere.

---

## What Are Claude Code Skills?

Skills are prompt files that extend Claude Code with custom slash commands. Drop a skill into your `.claude/skills/` folder and it becomes available as `/skill-name` in any Claude Code session.

Each skill in this library is a self-contained agent with defined tools, steps, and output formats. Some skills are standalone; others are designed to plug into a larger orchestration pipeline.

---

## Installation

Clone this repo and copy the skills you want into your Claude Code skills directory:

```bash
git clone https://github.com/RashiD2801/AI-Skills.git

# Copy a single skill
cp -r AI-Skills/concept-explorer ~/.claude/skills/

# Or copy everything
cp -r AI-Skills/* ~/.claude/skills/
```

Then restart Claude Code (or reload the window). Skills appear as `/skill-name` in your session.

> **Windows users:** Your Claude Code skills directory is typically `C:\Users\<you>\.claude\skills\`

---

## Skills Overview

### Startup Suite

A full startup validation and build pipeline — run individually or let `startup-analyzer` orchestrate everything.

| Skill | Command | What it does |
|---|---|---|
| **Startup Analyzer** | `/startup-analyzer "your idea"` | Master orchestrator — runs all 8 specialist agents in order with 2 user checkpoints, produces a full tabbed HTML report |
| **Market Research** | `/startup-market-research` | TAM/SAM/SOM breakdown (India primary), problem statement, ICP profiles, gap analysis, 2–3 pivot options |
| **Competitor Scan** | `/startup-competitor-scan` | Live web search for real global + India-specific competitors, feature gaps, pricing, user complaints, whitespace |
| **Financial Feasibility** | `/startup-financial-feasibility` | MVP cost (India rates), team & burn rate, revenue models, Year 1–3 projections, break-even (INR primary) |
| **Tech Feasibility** | `/startup-tech-feasibility` | Complexity score, build-vs-buy analysis, technical risks, team requirements, MVP timeline, GREEN/AMBER/RED verdict |
| **Tech Stack** | `/startup-tech-stack` | Opinionated frontend/backend/DB/AI-ML/infra recommendations with justifications and cost estimates |
| **Go-to-Market** | `/startup-go-to-market` | India-first launch plan, MVP scope, pricing strategy, distribution channels, phased expansion roadmap |
| **Architecture Diagram** | `/startup-architecture-diagram` | Self-contained HTML/CSS/SVG architecture diagram showing all components, services, and data flows |
| **Report Maker** | `/startup-report-maker` | Assembles all outputs into a beautiful 8-tab interactive HTML report with a BUILD/PIVOT/PASS verdict card |
| **Dev Kickstart** | `/startup-dev-kickstart` | Generates README, PRD, CLAUDE.md, ARCHITECTURE.md, STACK.md, ROADMAP.md — then creates and pushes a GitHub repo |
| **Pitch Deck** | `/startup-pitch-deck` | Investor or user pitch deck (.pptx) — 15–30 slides with data, charts, financials, and a clean design system |

### Development Tools

| Skill | Command | What it does |
|---|---|---|
| **Software Developer** | `/software-developer "what to build"` | Skip business validation — goes straight to tech feasibility → stack → architecture → dev kickstart. For builders who already know what they're building. |
| **System Architect** | `/system-architect` | Interview-driven: describe your system, get a self-contained HTML file with an SVG flow diagram and tech stack recommendation |

### Learning

| Skill | Command | What it does |
|---|---|---|
| **Concept Explorer** | `/concept-explorer "concept"` | Generates a self-contained HTML study page with explanations, analogies, curated videos/articles, real-world tools, and hands-on app ideas. Saved to `D:\AI\Learning\` |

---

## How the Startup Pipeline Works

```
/startup-analyzer "your idea"
         │
         ├── Phase 1 (parallel)
         │     ├── Market Research       ← TAM, ICP, pivots
         │     └── Competitor Scan       ← live web search
         │
         ├── Checkpoint 1 — proceed / pivot / stop?
         │
         ├── Phase 2 (parallel)
         │     ├── Financial Feasibility ← costs, projections, break-even
         │     └── Tech Feasibility      ← complexity, risks, verdict
         │
         ├── Checkpoint 2 — proceed / pivot / stop?
         │
         ├── Phase 3 (sequential)
         │     ├── Tech Stack            ← specific technology choices
         │     ├── Go-to-Market          ← launch strategy
         │     └── Architecture Diagram  ← system diagram
         │
         ├── Phase 4 — Report Maker      ← tabbed HTML report
         ├── Phase 5 — Dev Kickstart     ← GitHub repo + docs
         └── Phase 6 (optional) — Pitch Deck
```

Each phase agent also works standalone — you can run just `/startup-market-research` or just `/startup-tech-stack` if you only need a slice of the analysis.

---

## Output

All generated files land in predictable locations:

| Output | Location |
|---|---|
| Startup reports | `D:\AI\Startups\[slug]-[date].html` |
| Dev repo docs | `D:\AI\Startups\[slug]\` |
| Pitch decks | `D:\AI\Startups\[slug]\[slug]-pitch-deck-[date].pptx` |
| Learning pages | `D:\AI\Learning\learn-[concept].html` |

> These paths are Windows-specific and match my local setup. If you're on Mac/Linux, edit the `Write` steps in each `SKILL.md` to use your preferred paths.

---

## Design System

All generated HTML reports and pages use a consistent visual language:

- **Font:** DM Sans (Google Fonts)
- **Background:** `#F9F6F1` (off-white)
- **Primary text:** `#1B3A2D` (deep forest green)
- **Accent:** `#4A7C6F` (muted teal)
- **Highlight:** `#F0A882` (peach)
- **CTA:** `#E8724A` (strong orange)

---

## Skill Structure

Each skill lives in its own folder:

```
skill-name/
└── SKILL.md      ← the skill definition (frontmatter + step-by-step instructions)
```

`SKILL.md` uses YAML frontmatter to declare the skill name, description, and allowed tools, followed by step-by-step instructions for the agent.

---

## License

MIT — use freely, modify to fit your context, and share improvements.
