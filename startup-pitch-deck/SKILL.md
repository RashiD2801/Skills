---
name: startup-pitch-deck
description: Generate a stunning investor or user pitch deck (.pptx) from startup analysis data. Strong visuals, bold data, charts, and clean design. Up to 30 slides — more slides, less per slide. Saves to D:\AI\Startups\[slug]\ and pushes to GitHub. Usable standalone or as Step 10 of the startup-analyzer pipeline.
allowed-tools: Write, Bash, AskUserQuestion
---

# Startup Pitch Deck Generator

You are a world-class pitch deck designer and storyteller. You create decks that investors fund and users love — not because they're pretty, but because every slide makes one unmissable point and then gets out of the way. You use data ruthlessly. You write headlines that could stand alone as tweets. You never pad.

---

## Step 1 — Parse input

Extract from args:
- **IDEA:** the startup concept (post-pivot if applicable)
- **AUDIENCE:** `investor` or `user` (default: `investor`)
- **PIVOT:** pivot taken (if any) — optional
- **MARKET_CONTEXT:** AGENT_OUTPUT JSON from market-research — optional
- **COMPETITOR_CONTEXT:** AGENT_OUTPUT JSON from competitor-scan — optional
- **FINANCIAL_CONTEXT:** AGENT_OUTPUT JSON from financial-feasibility — optional
- **TECH_FEASIBILITY_CONTEXT:** AGENT_OUTPUT JSON from tech-feasibility — optional
- **TECH_STACK_CONTEXT:** AGENT_OUTPUT JSON from tech-stack — optional
- **GTM_CONTEXT:** AGENT_OUTPUT JSON from go-to-market — optional
- **VERDICT:** BUILD/PIVOT/PASS and confidence — optional

---

## Step 2 — Standalone mode: gather context

If running standalone (invoked directly via `/startup-pitch-deck`), ask:

Use AskUserQuestion:

**Question 1:** "Who is this pitch deck for?"
Options:
1. "Investors — fundraising / VC / angel pitch" → set AUDIENCE = investor
2. "Users — product demo / onboarding / sales pitch" → set AUDIENCE = user

Then ask:

**Question 2:** "Paste your startup idea and any analysis you have (market data, financials, tech stack, competitor scan). The more you give me, the stronger the deck."
(Free text — accept whatever they paste. Proceed with what's available. Mark missing sections as estimated.)

---

## Step 3 — Choose deck structure based on audience and project depth

### Scoring available data:
Count how many of these are available: MARKET_CONTEXT, COMPETITOR_CONTEXT, FINANCIAL_CONTEXT, TECH_FEASIBILITY_CONTEXT, TECH_STACK_CONTEXT, GTM_CONTEXT.

- 0–2 available → 15–18 slides (lean deck)
- 3–4 available → 20–24 slides (standard deck)
- 5–6 available → 26–30 slides (full deck)

### INVESTOR DECK — Slide outline (scale up/down within ranges):

```
COVER SECTION (2 slides)
  1.  Title Slide            — Company name, tagline, presenter, date
  2.  The One-Line Pitch     — Single sentence. What you do, for whom, and the outcome.

PROBLEM SECTION (3–4 slides)
  3.  The Problem            — One pain, stated in the customer's words. Data point(s).
  4.  Why Now                — Market shift, technology unlock, or regulatory change making this the right moment
  5.  Who Has This Problem   — ICP card: role, company size, geography, daily pain
  6.  The Cost of Inaction   — What it costs them TODAY (time, money, risk) to NOT have this

SOLUTION SECTION (3–5 slides)
  7.  Introducing [Product]  — The solution in one sentence + hero visual/diagram
  8.  How It Works           — 3-step process diagram (Step 1 → Step 2 → Step 3)
  9.  Product Demo / UI      — Key screen(s) or feature highlight (placeholder if no screenshots)
  10. Before vs. After       — Side-by-side comparison: old workflow vs. new workflow
  11. Key Differentiator     — The ONE thing competitors cannot easily copy

MARKET SECTION (3–4 slides)
  12. Market Size            — TAM / SAM / SOM chart. India primary, global context.
  13. Target Beachhead       — Specific first segment to capture, why, how big
  14. Competitive Landscape  — 2×2 matrix: axes chosen to show your unique position
  15. Why We Win             — Moat: data, network effects, switching costs, brand, tech

BUSINESS MODEL SECTION (3–4 slides)
  16. Business Model         — How you make money. Revenue model + pricing tiers.
  17. Unit Economics         — LTV, CAC, LTV:CAC ratio, payback period
  18. Traction               — Current metrics: users, MRR, growth rate, pilots, LOIs
  19. Growth Strategy        — Channel breakdown: India launch → regional → global

FINANCIALS SECTION (2–3 slides)
  20. Financial Projections  — Year 1–3: revenue, burn, headcount (bar/line chart)
  21. Use of Funds           — Pie chart: what you're raising, how it's split
  22. Path to Break-Even     — Monthly chart showing burn declining + revenue crossing

TEAM + ASK SECTION (3–4 slides)
  23. Team                   — Founders + key hires. One line each. Why us?
  24. Advisors / Backers     — If any. Logos or names.
  25. The Ask                — Raise amount, round type, lead/co-lead, use of funds summary
  26. Why Now + Why Us       — Conviction slide: the 3 reasons to write the cheque today
  27. Contact + Next Steps   — Email, LinkedIn, calendly or meeting CTA
```

Scale to 15–30 by adding/removing slides from the middle sections. Never remove: Title, The Problem, How It Works, Market Size, Business Model, Financials, The Ask, Contact.

---

### USER DECK — Slide outline (scale within ranges):

```
COVER SECTION (2 slides)
  1.  Title Slide             — Product name, tagline, short URL
  2.  "Sound familiar?"       — Relatable opening: describe their current pain

PROBLEM SECTION (2–3 slides)
  3.  The Problem You Face    — Their daily frustration, with data/stat
  4.  What You're Doing Today — Current workaround and why it's painful/slow/expensive
  5.  What This Costs You     — Time, money, or risk quantified

SOLUTION SECTION (4–6 slides)
  6.  There's a Better Way    — Product name + single benefit statement
  7.  How It Works            — 3-step visual flow
  8.  Feature 1               — One feature per slide: name + benefit + visual
  9.  Feature 2               — Same structure
  10. Feature 3               — Same structure
  11. Before vs. After        — Their old life vs. their new life

PROOF SECTION (2–3 slides)
  12. Who Uses It             — Customer logos or user personas
  13. What They Say           — 2–3 quotes / testimonials / case stats
  14. Results in Numbers      — Key outcome metric (e.g. "saves 5 hours/week")

PRICING + CTA SECTION (3–4 slides)
  15. Pricing                 — Plans table: Free / Starter / Pro / Enterprise
  16. Getting Started         — 3-step onboarding: Sign up → Connect → Go
  17. FAQ                     — Top 3 objections answered
  18. Let's Begin             — CTA slide: URL, QR code, email, offer
```

Scale to 15–25 by adding product deep-dive slides or use case examples.

---

## Step 4 — Extract and prepare content

From all available context JSONs, extract:

```
idea_name          → from IDEA
tagline            → derive a punchy 8-word max tagline
problem_statement  → from MARKET_CONTEXT.problem_statement
market_gap         → from MARKET_CONTEXT.market_gap
india_tam          → from MARKET_CONTEXT.india_tam
global_tam         → from MARKET_CONTEXT.global_tam
sam                → from MARKET_CONTEXT.sam (or estimate 10% of TAM)
som                → from MARKET_CONTEXT.som (or estimate 1–5% of SAM)
icp_1              → from MARKET_CONTEXT.icp_profiles[0]
icp_2              → from MARKET_CONTEXT.icp_profiles[1]
competitive_risk   → from COMPETITOR_CONTEXT.competitive_risk
whitespace         → from COMPETITOR_CONTEXT.whitespace
competitors        → from COMPETITOR_CONTEXT.competitors (top 3)
mvp_cost_inr       → from FINANCIAL_CONTEXT.mvp_cost_inr
monthly_burn_inr   → from FINANCIAL_CONTEXT.monthly_burn_inr
break_even         → from FINANCIAL_CONTEXT.break_even_customers
yr1_revenue_inr    → from FINANCIAL_CONTEXT.projections.base.year_1_revenue_inr
yr2_revenue_inr    → from FINANCIAL_CONTEXT.projections.base.year_2_revenue_inr
yr3_revenue_inr    → from FINANCIAL_CONTEXT.projections.base.year_3_revenue_inr
pricing_india      → from FINANCIAL_CONTEXT.india_price_per_month_inr
revenue_model      → from FINANCIAL_CONTEXT.recommended_revenue_model
tech_complexity    → from TECH_FEASIBILITY_CONTEXT.complexity_score
mvp_build_months   → from TECH_FEASIBILITY_CONTEXT.build_time_mvp_months
tech_verdict       → from TECH_FEASIBILITY_CONTEXT.tech_verdict
mvp_features       → from GTM_CONTEXT.mvp_features or TECH_FEASIBILITY_CONTEXT.mvp_features
expansion_order    → from GTM_CONTEXT.expansion_order
primary_channels   → from GTM_CONTEXT.primary_india_channels
```

For any missing field: use a sensible placeholder like `[~₹X Cr — estimate]` or derive from context. Never leave blanks.

---

## Step 5 — Generate the Python script and run it

Write a complete Python script to `D:\AI\Startups\pitch_deck_builder_temp.py` that:

1. Installs `python-pptx` if not present (using `subprocess` + `sys`)
2. Creates a `Presentation()` object
3. Defines the design system (colors, fonts, sizes)
4. Generates every slide in the chosen outline
5. Saves to `D:\AI\Startups\[slug]\[slug]-pitch-deck-[AUDIENCE]-[date].pptx`

### Design system (apply to ALL slides):

```python
# Color palette
COLOR_BG_DARK    = "1B3A2D"   # Deep forest green — title slides, section dividers
COLOR_BG_LIGHT   = "F9F6F1"   # Off-white — content slides
COLOR_ACCENT     = "4A7C6F"   # Muted teal — borders, highlights, subheadings
COLOR_WARM       = "F0A882"   # Peach/coral — callout boxes, emphasis
COLOR_CTA        = "E8724A"   # Strong orange — CTA buttons, key numbers
COLOR_TEXT_DARK  = "1B3A2D"   # Dark green for body text on light bg
COLOR_TEXT_LIGHT = "FFFFFF"   # White for text on dark bg
COLOR_CHART_1    = "2D6A4F"   # Deep green — primary chart bars
COLOR_CHART_2    = "4A7C6F"   # Teal — secondary bars
COLOR_CHART_3    = "E8724A"   # Orange — accent bars
COLOR_CHART_4    = "F0A882"   # Peach — soft series
COLOR_GOLD       = "D4860A"   # Amber — warnings, PIVOT indicator
COLOR_RED        = "C0392B"   # Red — PASS indicator, risk flags
COLOR_BORDER     = "B0C4C1"   # Muted teal-grey — dividers

# Slide dimensions: 16:9 widescreen
SLIDE_WIDTH  = Inches(13.33)
SLIDE_HEIGHT = Inches(7.5)

# Fonts: use Calibri (always available); set bold + size for impact
FONT_HERO    = 60   # Massive headline on title slides
FONT_H1      = 40   # Main slide headline
FONT_H2      = 28   # Section header / sub-headline  
FONT_H3      = 22   # Sub-section / label
FONT_BODY    = 18   # Body text
FONT_CAPTION = 14   # Labels, footnotes, data labels
FONT_SMALL   = 12   # Fine print, source citations
```

### Slide anatomy rules:
- **Title slides (dark bg):** Full dark background (`COLOR_BG_DARK`), headline in white at FONT_HERO or FONT_H1, tagline in `COLOR_WARM`, left-aligned or centred
- **Content slides (light bg):** Off-white background, top-left headline in `COLOR_TEXT_DARK` at FONT_H1 (bold), left accent bar (thin rectangle, `COLOR_ACCENT`, full height, 0.12" wide), body content below headline, bottom-left slide number in `COLOR_ACCENT` at FONT_SMALL
- **Section dividers:** Dark bg, large section number in `COLOR_WARM` (FONT_HERO, semi-transparent), section name in white (FONT_H1)
- **Data slides:** Large stat in `COLOR_CTA` at FONT_HERO, label below it in FONT_H3, `COLOR_TEXT_DARK`. Multiple stats in a row of evenly spaced boxes with `COLOR_ACCENT` border.
- **Callout boxes:** Rounded rectangle, `COLOR_WARM` fill, `COLOR_TEXT_DARK` text, FONT_H3 — used for key insight, whitespace, or quote
- **Chart slides:** Use `python-pptx` charts (BAR_CLUSTERED, LINE, PIE). Title above chart in FONT_H2. Data labels on bars/lines in FONT_CAPTION. Legend below chart.
- **Table slides:** Header row in `COLOR_BG_DARK` (white text, FONT_H3). Alternating rows: `COLOR_BG_LIGHT` / slightly darker (`E8E4DF`). Borders in `COLOR_BORDER`.
- **Two-column slides:** Left column = `COLOR_BG_DARK` strip (45% width) with white text, right column = `COLOR_BG_LIGHT` with dark text. Used for Before/After, Problem/Solution comparisons.

### Slide-specific implementation:

**TITLE SLIDE:**
- Background: solid dark green rectangle covering full slide
- Company name: FONT_HERO, white, bold, centred, vertically mid
- Tagline: FONT_H2, `COLOR_WARM`, centred, below name
- Bottom strip: thin `COLOR_WARM` line, then presenter name + date in FONT_CAPTION, white

**THE PROBLEM (and similar single-focus slides):**
- ONE large headline stat (e.g. "₹2,400 Cr lost annually") in `COLOR_CTA` at 80pt, centred
- 2-line explanation in FONT_H3 below
- 3 bullet pain points as icon-adjacent text blocks (use unicode bullets ▸ or numbered)
- Bottom callout box: market gap or key insight

**HOW IT WORKS (3-step process):**
- Three boxes left-to-right, each: `COLOR_ACCENT` border, rounded, number in `COLOR_CTA` (large), step title bold FONT_H2, 2-line description FONT_BODY
- Arrow shapes (→) between boxes in `COLOR_WARM`
- Horizontal layout, equal spacing

**MARKET SIZE (TAM/SAM/SOM):**
- Three concentric-style boxes or a 3-bar chart (manual shapes for visual effect)
- TAM = largest box/bar in `COLOR_CHART_1`, SAM in `COLOR_CHART_2`, SOM in `COLOR_CHART_3`
- Values in `COLOR_CTA` (bold, FONT_H2) with labels below
- Note: India TAM as headline stat top-right

**COMPETITIVE LANDSCAPE (2×2 matrix):**
- Draw 2 axes using lines, label axes (e.g. "India-Focused ↔ Global" vs "Specific ↔ General")
- Place competitor name labels at their positions (simple text boxes)
- YOUR COMPANY in a filled circle (`COLOR_CTA`), white text, larger than competitors
- Quadrant labels in corners (FONT_SMALL, `COLOR_ACCENT`)

**FINANCIAL PROJECTIONS:**
- Grouped bar chart: Year 1, Year 2, Year 3
- Revenue (green), Burn (orange) side by side
- Break-even point shown as vertical dotted line
- Values as data labels

**USE OF FUNDS:**
- Pie chart with `COLOR_CHART_1`, `COLOR_CHART_2`, `COLOR_CHART_3`, `COLOR_CHART_4`
- Each slice labelled with category + percentage
- Legend to right

**THE ASK:**
- Dark background slide
- Raise amount in FONT_HERO, `COLOR_WARM`, centred
- Round type in FONT_H2, white
- Three use-of-funds bullets with icons (unicode: ◆)
- Bottom: milestones this raise achieves (3 items, FONT_BODY)

**CONTACT / CTA:**
- Dark background
- "Let's build this together." in FONT_H1, white
- Email, LinkedIn, website in `COLOR_WARM`, FONT_H2
- QR code placeholder box (labelled "QR — meeting link") in bottom right

### Python script structure:

```python
import sys
import subprocess

# Auto-install python-pptx
try:
    from pptx import Presentation
    from pptx.util import Inches, Pt, Emu
    from pptx.dml.color import RGBColor
    from pptx.enum.text import PP_ALIGN
    from pptx.enum.chart import XL_CHART_TYPE
    from pptx.chart.data import ChartData
    import pptx.oxml.ns as nsmap
    from lxml import etree
except ImportError:
    subprocess.check_call([sys.executable, "-m", "pip", "install", "python-pptx"])
    from pptx import Presentation
    from pptx.util import Inches, Pt, Emu
    from pptx.dml.color import RGBColor
    from pptx.enum.text import PP_ALIGN
    from pptx.enum.chart import XL_CHART_TYPE
    from pptx.chart.data import ChartData

# ── DESIGN SYSTEM ─────────────────────────────────────────────────
[define all colors as RGBColor objects]
[define helper functions: add_slide, set_bg, add_textbox, add_shape, add_chart, add_table, add_two_col]

# ── HELPERS ────────────────────────────────────────────────────────

def hex_rgb(hex_str):
    h = hex_str.lstrip('#')
    return RGBColor(int(h[0:2],16), int(h[2:4],16), int(h[4:6],16))

def add_filled_rect(slide, left, top, width, height, fill_hex, border_hex=None):
    shape = slide.shapes.add_shape(MSO_SHAPE_TYPE.RECTANGLE, left, top, width, height)  
    # Note: use pptx.util.MSO_SHAPE_TYPE or equivalent
    shape.fill.solid()
    shape.fill.fore_color.rgb = hex_rgb(fill_hex)
    if border_hex:
        shape.line.color.rgb = hex_rgb(border_hex)
        shape.line.width = Pt(1.5)
    else:
        shape.line.fill.background()
    return shape

def add_textbox(slide, text, left, top, width, height, font_size, bold=False, 
                color_hex="1B3A2D", align=PP_ALIGN.LEFT, italic=False, wrap=True):
    txBox = slide.shapes.add_textbox(left, top, width, height)
    tf = txBox.text_frame
    tf.word_wrap = wrap
    p = tf.paragraphs[0]
    p.alignment = align
    run = p.add_run()
    run.text = text
    run.font.size = Pt(font_size)
    run.font.bold = bold
    run.font.italic = italic
    run.font.color.rgb = hex_rgb(color_hex)
    run.font.name = "Calibri"
    return txBox

def set_slide_bg(slide, color_hex):
    background = slide.background
    fill = background.fill
    fill.solid()
    fill.fore_color.rgb = hex_rgb(color_hex)

def add_slide_number(slide, number, total):
    add_textbox(slide, f"{number} / {total}", 
                Inches(12.5), Inches(7.1), Inches(0.7), Inches(0.3),
                font_size=10, color_hex="4A7C6F", align=PP_ALIGN.RIGHT)

def add_accent_bar(slide):
    """Vertical left accent bar for content slides"""
    bar = slide.shapes.add_shape(1, Inches(0), Inches(0), Inches(0.12), Inches(7.5))
    bar.fill.solid()
    bar.fill.fore_color.rgb = hex_rgb("4A7C6F")
    bar.line.fill.background()

def add_slide_headline(slide, text, top=Inches(0.4)):
    add_textbox(slide, text,
                Inches(0.25), top, Inches(12.5), Inches(1.0),
                font_size=36, bold=True, color_hex="1B3A2D")

# ── ALL SLIDE FUNCTIONS ────────────────────────────────────────────
[implement one function per slide type, following the design rules above]
[each function: def slide_XX_name(prs, data): ...]

# ── MAIN ──────────────────────────────────────────────────────────
prs = Presentation()
prs.slide_width = Inches(13.33)
prs.slide_height = Inches(7.5)

# Call slide functions in order
[call each slide function, passing prs and the data dict]

# Save
output_path = r"D:\AI\Startups\[slug]\[slug]-pitch-deck-[audience]-[date].pptx"
prs.save(output_path)
print(f"SAVED: {output_path}")
```

**CRITICAL:** Write the complete, executable Python script with ALL slides fully implemented (no placeholders like `# TODO` or `pass`). Every slide must have real content from the extracted data. The script must run without errors.

Use `from pptx.util import Inches, Pt` — no `Emu` needed for most operations.
Use `slide.shapes.add_shape(MSO_SHAPE_TYPE.FREEFORM, ...)` only if needed; prefer `add_shape(1, ...)` (rectangle) for backgrounds and boxes.
Import `from pptx.enum.shapes import MSO_SHAPE_TYPE` for shape constants.

---

## Step 6 — Execute the script

Run:
```bash
python "D:\AI\Startups\pitch_deck_builder_temp.py"
```

If the script errors:
- Read the traceback
- Fix the script (Write to the same path, overwrite)
- Re-run
- Repeat until the file is successfully saved (max 3 attempts)

After success:
```bash
del "D:\AI\Startups\pitch_deck_builder_temp.py"
```

---

## Step 7 — Push to GitHub

```bash
cd "D:\AI\Startups"
git add "[slug]/[slug]-pitch-deck-[audience]-[date].pptx"
git commit -m "Add: [audience] pitch deck — [idea name]"
git push
```

If the slug subfolder doesn't exist yet (standalone run, no dev kickstart done):
```bash
cd "D:\AI\Startups"
git add "[slug]-pitch-deck-[audience]-[date].pptx"
git commit -m "Add: [audience] pitch deck — [idea name]"
git push
```

Adjust path accordingly.

---

## Step 8 — Report to user

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  PITCH DECK READY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Idea:      [idea name]
  Audience:  [Investor / User]
  Slides:    [N] slides
  File:      D:\AI\Startups\[slug]\[filename].pptx
  GitHub:    pushed to Ideas-and-systems
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Slide sections:
    [list section names and slide ranges]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Nothing else. Open the file in PowerPoint or Google Slides.
