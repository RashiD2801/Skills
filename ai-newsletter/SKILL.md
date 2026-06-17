---
name: ai-newsletter
description: Curate recent AI news (not already in past newsletters) into a personal digest focused on AI in architecture, urban design, allied fields, and general AI. Saves as HTML and PDF in D:\AI\News and Updates\YYYY-MM-DD\. Includes journal/blog highlights and reflection questions. Trigger phrases include "run the AI newsletter", "give me today's AI digest", "any new AI news", "/ai-newsletter".
allowed-tools: WebSearch, WebFetch, Write, Bash
---

# AI Newsletter

Curate recent AI news that has not been covered in the last 3 newsletters, format it as a clean digest, and save it as HTML and PDF.

---

## Step 1 - Determine the Date Range and Past Headlines

Run these two tasks in parallel:

**Task A - Find the date range:**
Use Bash to list the dated folders in `D:\AI\News and Updates\`:

```powershell
Get-ChildItem "D:\AI\News and Updates\" -Directory | Where-Object { $_.Name -match '^\d{4}-\d{2}-\d{2}$' } | Sort-Object Name -Descending | Select-Object -First 3 -ExpandProperty Name
```

- Take the most recent folder date as the "from date" (news must be published after this date).
- If no folders exist, the from date is 7 days ago.
- Never go back more than 7 days regardless.
- The "to date" is today.

**Task B - Extract past headlines to avoid duplicates:**
Read the HTML files from those same 3 most recent folders. Scan each file for text inside `class="story-headline"` and `class="top-story-headline"` tags, plus any `<title>` inside top-story sections. Build a list of known headlines and URLs to skip.

---

## Step 2 - Search for Recent AI News (All searches in parallel)

Use the from date and to date from Step 1 in your search queries. Append the date context (e.g. "after [Month YYYY]") to help surface fresh results.

**Architecture, Urban Design and Allied Fields:**
1. `AI architecture design tools software news after [from-month YYYY]`
2. `artificial intelligence urban planning smart city design [YYYY]`
3. `generative AI building design BIM parametric architecture [YYYY] latest`
4. `AI construction real estate interior design landscape heritage [YYYY]`

**General AI:**
5. `AI news latest announcements [Month YYYY]`
6. `new AI models tools launched released [YYYY]`
7. `AI research breakthroughs [YYYY]`
8. `AI startup product funding launch [YYYY]`

**Journals and Blogs:**
9. `Dezeen ArchDaily Metropolis "Architectural Record" AI article [YYYY]`
10. `OpenAI Anthropic Google DeepMind research blog post [Month YYYY]`
11. `AI design urban architecture blog insight [YYYY]`

Collect all results. For each story note: headline, source name, URL, publication date, and a 1-sentence summary.

---

## Step 3 - Filter and Rank Stories

**Remove:** Any story whose headline or URL appears in the list of past headlines from Step 1. Any story published before the from date. Any story without a verifiable URL.

**Rank remaining stories by priority:**

| Tier | Category | Minimum to include |
|------|----------|--------------------|
| 1 | AI in architecture, urban design, built environment | As many as found, minimum 1 |
| 2 | Allied fields: interior design, real estate, construction, landscape, heritage conservation | 1 if available |
| 3 | General AI: models, tools, research, startups, policy | 2 to 4 |
| 4 | Journal and blog highlights: Dezeen, ArchDaily, Metropolis, OpenAI blog, DeepMind blog, Anthropic | 1 to 3 if available |

There is no minimum total. If only 2 stories are fresh and relevant, publish those 2. Do not pad with old news.

**Top Story** = the single most exciting or immediately useful item for a practising architect. Tier 1 gets priority, but a Tier 3 story wins if it is genuinely significant news.

---

## Step 4 - Write the Newsletter Text

Rules for writing:
- Tone: Friendly and direct. Like a thoughtful colleague sharing news in plain words.
- Max 2 sentences per story. If 1 sentence is enough, use 1.
- No filler phrases. Never write "In a groundbreaking development", "Experts say", or "It is worth noting".
- Headlines: Active voice, max 10 words.
- No emojis anywhere in the content.
- No em-dashes. Use a colon or a regular hyphen instead.
- After every story include the full article link on its own line.
- Source name goes in smaller attribution text after the link.

Newsletter structure to follow exactly:

```
YOUR AI DIGEST
[DD Month YYYY]

Hi Rashi,

Here is what is new in AI since your last digest.

-----

TOP STORY

[SHORT PUNCHY HEADLINE]
[2 sentences. What happened. Why it matters to an architect right now.]
Read the full story: [URL]
Source: [Source Name] - [Publication Date]

-----

AI IN ARCHITECTURE AND DESIGN

[Headline]
[1-2 sentence summary.]
Read more: [URL]
Source: [Source Name] - [Publication Date]

[Repeat for each story in this section]

-----

AI IN CITIES AND THE BUILT ENVIRONMENT

[Headline]
[1-2 sentence summary.]
Read more: [URL]
Source: [Source Name] - [Publication Date]

[Repeat]

-----

GENERAL AI

[Headline]
[1-2 sentence summary.]
Read more: [URL]
Source: [Source Name] - [Publication Date]

[Repeat]

-----

FROM THE JOURNALS AND BLOGS

[Only include this section if relevant stories were found in Step 3 Tier 4. Skip entirely if none.]

[Headline or Title]
[1-2 sentences on what the piece argues or covers and why it is worth reading.]
Read it here: [URL]
Source: [Source Name] - [Publication Date]

[Repeat for up to 3 entries]

-----

QUESTIONS TO THINK ABOUT

[Write 2 to 3 short reflection questions based on this digest's specific stories. They should prompt the reader to think about how the news applies to their own architecture or design practice. Keep each question to one sentence. Do not make them generic.]

1. [Specific reflection question tied to a story in this digest]
2. [Specific reflection question tied to a story in this digest]
3. [Optional third question if a third story warrants it]

-----

That is your digest for this period.
```

Replace all placeholders with real content. Remove any section that has no stories.

---

## Step 5 - Create the Output Folder

Create the folder for today's date:

```powershell
New-Item -ItemType Directory -Force -Path "D:\AI\News and Updates\YYYY-MM-DD"
```

Replace YYYY-MM-DD with today's actual date.

---

## Step 6 - Write the Styled HTML File

Write the file to:
`D:\AI\News and Updates\YYYY-MM-DD\AI-Digest-YYYY-MM-DD.html`

Use this exact HTML structure. Replace all placeholders with real content. Remove any HTML comment lines from the final file. Skip entire sections (including their `<hr>` divider) if that section has no stories.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AI Digest - [DD Month YYYY]</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;0,9..40,600;1,9..40,400&display=swap" rel="stylesheet">
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: 'DM Sans', 'Segoe UI', system-ui, sans-serif;
      background: #F7F6F3;
      color: #1B2A22;
      font-size: 16px;
      line-height: 1.7;
    }
    .wrapper {
      max-width: 680px;
      margin: 48px auto;
      background: #FFFFFF;
      border: 1px solid #E2DDD6;
    }
    .header {
      background: #1B3A2D;
      padding: 40px 48px 32px;
    }
    .header-label {
      font-size: 11px;
      font-weight: 600;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: #7FAF96;
      margin-bottom: 8px;
    }
    .header-title {
      font-size: 26px;
      font-weight: 600;
      color: #FFFFFF;
      line-height: 1.25;
    }
    .header-date {
      margin-top: 10px;
      font-size: 13px;
      color: #7FAF96;
      font-weight: 400;
    }
    .content {
      padding: 40px 48px;
    }
    .greeting {
      font-size: 16px;
      color: #4A5C52;
      margin-bottom: 32px;
    }
    .divider {
      border: none;
      border-top: 1px solid #E2DDD6;
      margin: 32px 0;
    }
    .section-label {
      font-size: 10px;
      font-weight: 700;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      color: #4A7C6F;
      margin-bottom: 20px;
    }
    .top-story {
      background: #F0F6F2;
      border-left: 4px solid #4A7C6F;
      padding: 24px;
    }
    .top-story-headline {
      font-size: 18px;
      font-weight: 600;
      color: #1B3A2D;
      margin-bottom: 10px;
      line-height: 1.35;
    }
    .top-story-body {
      font-size: 15px;
      color: #3A4A42;
      margin-bottom: 14px;
    }
    .story-link {
      display: block;
      margin-top: 6px;
    }
    .story-link a {
      font-size: 13px;
      color: #4A7C6F;
      text-decoration: none;
      font-weight: 500;
      word-break: break-all;
    }
    .story-link a:hover { text-decoration: underline; }
    .story-source {
      font-size: 11px;
      color: #A0A8A4;
      margin-top: 4px;
      font-weight: 400;
    }
    .story-block {
      padding: 18px 0;
      border-bottom: 1px solid #EEE9E2;
    }
    .story-block:last-child { border-bottom: none; }
    .story-headline {
      font-size: 15px;
      font-weight: 600;
      color: #1B3A2D;
      margin-bottom: 6px;
    }
    .story-body {
      font-size: 15px;
      color: #3A4A42;
      margin-bottom: 6px;
    }
    .journal-block {
      background: #F9F7F4;
      border: 1px solid #E2DDD6;
      padding: 20px;
      margin-bottom: 14px;
    }
    .journal-headline {
      font-size: 15px;
      font-weight: 600;
      color: #1B3A2D;
      margin-bottom: 6px;
    }
    .journal-body {
      font-size: 14px;
      color: #4A5C52;
      margin-bottom: 8px;
    }
    .questions-section {
      background: #1B3A2D;
      padding: 28px 32px;
      margin-top: 32px;
    }
    .questions-label {
      font-size: 10px;
      font-weight: 700;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      color: #7FAF96;
      margin-bottom: 16px;
    }
    .question-item {
      font-size: 15px;
      color: #D4E8DC;
      margin-bottom: 12px;
      padding-left: 20px;
      position: relative;
      line-height: 1.6;
    }
    .question-item::before {
      content: counter(q-counter) ".";
      counter-increment: q-counter;
      position: absolute;
      left: 0;
      color: #7FAF96;
      font-weight: 600;
    }
    .questions-list {
      counter-reset: q-counter;
    }
    .footer {
      background: #F0F4F2;
      padding: 24px 48px;
      font-size: 13px;
      color: #8E9E98;
      border-top: 1px solid #E2DDD6;
    }
    @media print {
      body { background: #FFFFFF; }
      .wrapper { margin: 0; border: none; max-width: 100%; }
    }
  </style>
</head>
<body>
  <div class="wrapper">
    <div class="header">
      <div class="header-label">Personal AI Digest</div>
      <div class="header-title">Your AI News, Curated</div>
      <div class="header-date">[DD Month YYYY]</div>
    </div>

    <div class="content">
      <p class="greeting">Hi Rashi, here is what is new in AI since your last digest.</p>

      <!-- TOP STORY -->
      <div class="section-label">Top Story</div>
      <div class="top-story">
        <div class="top-story-headline">[TOP STORY HEADLINE]</div>
        <div class="top-story-body">[2 sentences. What happened. Why it matters to an architect.]</div>
        <div class="story-link"><a href="[URL]">Read the full story</a></div>
        <div class="story-source">[Source Name] - [Publication Date]</div>
      </div>

      <hr class="divider">

      <!-- AI IN ARCHITECTURE AND DESIGN -->
      <div class="section-label">AI in Architecture and Design</div>
      <div class="story-block">
        <div class="story-headline">[Headline]</div>
        <div class="story-body">[1-2 sentence summary]</div>
        <div class="story-link"><a href="[URL]">Read more</a></div>
        <div class="story-source">[Source Name] - [Publication Date]</div>
      </div>
      <!-- Repeat story-block for each story in this section -->

      <hr class="divider">

      <!-- AI IN CITIES AND THE BUILT ENVIRONMENT -->
      <div class="section-label">AI in Cities and the Built Environment</div>
      <div class="story-block">
        <div class="story-headline">[Headline]</div>
        <div class="story-body">[1-2 sentence summary]</div>
        <div class="story-link"><a href="[URL]">Read more</a></div>
        <div class="story-source">[Source Name] - [Publication Date]</div>
      </div>
      <!-- Repeat -->

      <hr class="divider">

      <!-- GENERAL AI -->
      <div class="section-label">General AI</div>
      <div class="story-block">
        <div class="story-headline">[Headline]</div>
        <div class="story-body">[1-2 sentence summary]</div>
        <div class="story-link"><a href="[URL]">Read more</a></div>
        <div class="story-source">[Source Name] - [Publication Date]</div>
      </div>
      <!-- Repeat -->

      <!-- FROM THE JOURNALS AND BLOGS - only include if there are relevant entries -->
      <hr class="divider">
      <div class="section-label">From the Journals and Blogs</div>
      <div class="journal-block">
        <div class="journal-headline">[Article or Post Title]</div>
        <div class="journal-body">[1-2 sentences on what it covers and why it is worth reading]</div>
        <div class="story-link"><a href="[URL]">Read it here</a></div>
        <div class="story-source">[Source Name] - [Publication Date]</div>
      </div>
      <!-- Repeat journal-block for up to 3 entries -->

      <!-- QUESTIONS TO THINK ABOUT -->
      <div class="questions-section">
        <div class="questions-label">Questions to Think About</div>
        <div class="questions-list">
          <div class="question-item">[Specific reflection question tied to a story in this digest]</div>
          <div class="question-item">[Specific reflection question tied to a story in this digest]</div>
          <!-- Add a third question-item only if a third story warrants it -->
        </div>
      </div>

    </div>

    <div class="footer">
      That is your digest for this period.
    </div>
  </div>
</body>
</html>
```

---

## Step 7 - Convert HTML to PDF Using Chrome

Write this Python script to `C:\Users\KRITIK\AppData\Local\Temp\gen_newsletter_pdf.py`. Replace ACTUAL_DATE with the real date string (YYYY-MM-DD), then run it with `python`.

```python
import subprocess
import os
import sys

date_str = "ACTUAL_DATE"
folder = f"D:\\AI\\News and Updates\\{date_str}"
html_path = os.path.join(folder, f"AI-Digest-{date_str}.html")
pdf_path = os.path.join(folder, f"AI-Digest-{date_str}.pdf")

chrome_paths = [
    r"C:\Program Files\Google\Chrome\Application\chrome.exe",
    r"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe",
    r"C:\Users\KRITIK\AppData\Local\Google\Chrome\Application\chrome.exe",
]

chrome = next((p for p in chrome_paths if os.path.exists(p)), None)

if not chrome:
    print("Chrome not found at standard paths. PDF skipped. HTML file is ready.")
    sys.exit(0)

cmd = [
    chrome,
    "--headless",
    "--disable-gpu",
    "--no-sandbox",
    f"--print-to-pdf={pdf_path}",
    "--print-to-pdf-no-header",
    html_path
]

result = subprocess.run(cmd, capture_output=True, text=True, timeout=30)

if os.path.exists(pdf_path):
    print(f"PDF saved: {pdf_path}")
else:
    print(f"PDF generation failed. HTML is still available at: {html_path}")
```

---

## Step 8 - Show a Windows Notification and Open the Digest in the Browser

After the files are saved, run this PowerShell command via Bash to show a system tray notification and open the HTML file in the default browser. Replace YYYY-MM-DD with the actual date.

```powershell
# Show Windows balloon tip notification
Add-Type -AssemblyName System.Windows.Forms
Add-Type -AssemblyName System.Drawing
$notify = New-Object System.Windows.Forms.NotifyIcon
$notify.Icon = [System.Drawing.SystemIcons]::Information
$notify.BalloonTipTitle = "AI Newsletter Ready"
$notify.BalloonTipText = "Your Monday AI digest is saved and opening now."
$notify.BalloonTipIcon = [System.Windows.Forms.ToolTipIcon]::Info
$notify.Visible = $true
$notify.ShowBalloonTip(8000)
Start-Sleep -Seconds 1
$notify.Dispose()

# Open the HTML file in the default browser
Start-Process "D:\AI\News and Updates\YYYY-MM-DD\AI-Digest-YYYY-MM-DD.html"
```

---

## Step 9 - Confirm and Ask Follow-Up Questions

First, reply with exactly this:

```
Digest saved for [DD Month YYYY].

Folder: D:\AI\News and Updates\YYYY-MM-DD\
Files: AI-Digest-YYYY-MM-DD.html and AI-Digest-YYYY-MM-DD.pdf

Top story: [TOP STORY HEADLINE]
```

Then ask the user up to 3 short follow-up questions to refine future digests. Base these on what you found or did not find in this run. Examples of the kind of questions to ask:

- "There was very little news this week on [specific topic]. Do you want me to broaden the search to include [related topic]?"
- "I found several stories on [specific software tool]. Should I keep covering that tool or skip it going forward?"
- "The journals section was empty this time. Are you happy for me to skip it when there is nothing fresh, or should I always include at least one entry even if it is a few weeks old?"

Keep each question to one sentence. Do not ask generic questions like "Was this helpful?"
