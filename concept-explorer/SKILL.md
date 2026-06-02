---
name: concept-explorer
description: Use this skill whenever the user wants to learn about a concept, technology, tool, framework, or term — especially in AI/ML, software, or anything tech-adjacent. Triggers include "explain X to me", "what is X", "help me understand X", "I keep seeing X everywhere", "teach me about X", "what does X mean", "I don't understand X".
allowed-tools: WebSearch, Write, Bash
---

# Concept Explorer

Generate a beautiful, self-contained HTML learning page for any concept the user wants to understand.

## Step 1 — Identify the concept

Extract the concept from the user's message. If ambiguous, ask one short clarifying question before proceeding.

## Step 2 — Research (run in parallel)

Before writing anything, search for:

1. **Real tools / apps using this concept** — search `"[concept] used in production apps tools 2024 2025"`
2. **YouTube explainer videos** — search `"[concept] explained YouTube"` — find 2–3 videos with real URLs, prefer channels like 3Blue1Brown, Fireship, Andrej Karpathy, Two Minute Papers, or high-quality tutorials
3. **Papers or articles** — search `"[concept] paper article blog explainer"` — find 1–2 links (arXiv, Distill.pub, Towards Data Science, official docs, or a strong blog post)

Only include links you found via search — never fabricate URLs.

## Step 3 — Generate the HTML file

Write a single self-contained HTML file directly to `D:\AI\Learning\learn-[concept-name].html` (kebab-case the concept name).

---

### Page sections (in order)

#### A. Hero header
- Large concept name, styled as a title
- One-sentence plain-English definition beneath it (no jargon)
- A subtle "study card" feel — not a generic docs page

#### B. Plain-language explanation
- 3–5 paragraphs, written like you're explaining to a smart non-specialist
- Use at least one real-world analogy that makes the concept tangible
- Break complex ideas into digestible steps
- No bullet points in this section — flowing prose only

#### C. Real-world tools using this concept
- 3–5 tools or products currently using this concept in production
- For each: name, what it does, and one sentence on how it uses the concept
- Display as styled cards

#### D. Watch & Read
- 2–3 YouTube videos (title + real URL from search)
- 1–2 papers or articles (title + real URL from search)
- Display as clean link cards with an icon prefix (▶ for video, ◆ for article)
- Never fabricate a link — if search didn't return a good result, omit that slot

#### E. Build with this — App Ideas
- 2–3 app ideas the user could build using this concept
- **Skew strongly toward**: architecture, urban design, interior design, real estate, and planning
- Mix of difficulty:
  - At least one "buildable now with Claude" (simple, no infra needed)
  - At least one aspirational (interesting, more complex)
- For each idea: a name, a 2-sentence description, and a difficulty tag (Quick Build / Weekend Project / Ambitious)
- Display as styled cards

#### F. Git push command
- Display a styled code block at the bottom of the HTML page showing the commands that were run (for reference)

---

### Design rules

**Colour palette — use exactly these:**

| Role | Hex |
|---|---|
| Primary text / dark fills | `#1B3A2D` |
| Section headers / accents | `#4A7C6F` |
| Borders / subtle dividers | `#B0C4C1` |
| Warm highlight (app idea cards) | `#F0A882` |
| CTA / tags / icons | `#E8724A` |
| Background | `#F9F6F1` (off-white) |
| Card background | `#FFFFFF` |

**Typography:**
- Font: DM Sans — load via `<link rel="preconnect" href="https://fonts.googleapis.com">` and `<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;0,9..40,600;1,9..40,400&display=swap" rel="stylesheet">` in `<head>`. Fallback stack: `font-family: 'DM Sans', 'Segoe UI', system-ui, sans-serif`
- Hero title: large, bold (600), `#1B3A2D`
- Section labels: small caps or uppercase tracking, `#4A7C6F`
- Body text: comfortable line-height (1.7), weight 400, `#1B3A2D`

**Layout:**
- Max content width: `860px`, centred
- Generous padding — this should feel like opening a well-designed magazine page
- Cards in a 2-column CSS grid on wider screens, 1-column on narrow
- Difficulty tags as small inline pills: Quick Build = `#4A7C6F`, Weekend Project = `#E8724A`, Ambitious = `#1B3A2D`

**HTML file rules:**
- All CSS in `<style>` in `<head>` (Google Fonts link is the only external dependency — acceptable)
- No JavaScript needed — pure HTML/CSS
- Must render correctly in any modern browser

---

## Step 4 — Push to GitHub

After writing the HTML file, run these git commands via Bash:

```
cd D:\AI\Learning
git add learn-[concept-name].html
git commit -m "Add: learn-[concept-name].html"
git push
```

## After pushing

Tell the user:
- The filename
- One sentence on the analogy you used
- Confirm the file was pushed to GitHub

Nothing more.
