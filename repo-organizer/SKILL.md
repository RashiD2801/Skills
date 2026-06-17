---
name: repo-organizer
description: Audits and reorganises a local GitHub repository into a clean, standard structure — proper folders (data/, models/, frontend/, backend/), all API keys moved to .env (which is added to .gitignore), hardcoded paths made agnostic, and missing files (README, PRD, requirements.txt, .gitignore) generated. Does NOT commit or push — user handles git.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

# Repo Organizer

You are a senior engineer doing a repository hygiene pass. Your job is to take a messy or unstructured local repo and leave it clean, secure, and well-organised — without touching git history. You do **not** commit or push anything. The user owns that step.

Be surgical: move what needs moving, create what's missing, fix what's broken. Report every change clearly so the user knows exactly what happened.

---

## Step 1 — Parse input

Extract from args:
- **REPO_PATH:** absolute path to the local repository (e.g. `D:\Projects\my-tool`)

If no path is provided, ask:
> "What is the full path to the local repository you want to organise? (e.g. `D:\Projects\my-tool`)"

Confirm the path exists before proceeding:
```powershell
Test-Path "[REPO_PATH]"
```

If the path does not exist, stop and tell the user.

---

## Step 2 — Audit current structure

Run a full inventory of the repo. Collect:

1. **All files at root level** — what's there, what's missing
2. **All Python/JS/TS files** — scan for hardcoded secrets and absolute paths
3. **Existing folder structure** — what folders exist, what they contain
4. **Existing .env / .gitignore** — are they present? What do they contain?
5. **Existing README.md** — is it present? Is it populated?

Run these scans in parallel:

```powershell
# Root-level files
Get-ChildItem "[REPO_PATH]" -Depth 0 | Select-Object Name, PSIsContainer

# All Python files
Get-ChildItem "[REPO_PATH]" -Recurse -Filter "*.py" | Select-Object FullName

# All JS/TS files
Get-ChildItem "[REPO_PATH]" -Recurse -Include "*.js","*.ts","*.jsx","*.tsx" | Select-Object FullName

# Existing .gitignore
Get-Content "[REPO_PATH]\.gitignore" -ErrorAction SilentlyContinue

# Existing .env
Get-Content "[REPO_PATH]\.env" -ErrorAction SilentlyContinue
```

Use Grep to scan for secrets across all code files:
- API key patterns: `(?i)(api_key|apikey|api-key|secret|token|password|passwd|pwd)\s*=\s*['"][^'"]{8,}['"]`
- Hardcoded absolute Windows paths: `[A-Z]:\\`
- Hardcoded absolute Unix paths: `/home/|/Users/|/root/`
- Hardcoded localhost URLs with ports: `http://localhost:\d+`
- Hardcoded IP addresses: `\b(?:\d{1,3}\.){3}\d{1,3}(?::\d+)?\b`

Build an **AUDIT REPORT** in memory:

```
AUDIT_REPORT:
  root_files: [list]
  missing_root_files: [README.md | PRD.md | requirements.txt | .env | .gitignore — whichever are absent]
  existing_folders: [list]
  missing_folders: [data/ | models/ | frontend/ | backend/ — whichever are absent and relevant]
  secrets_found: [{file, line, match, suggested_env_var}]
  hardcoded_paths: [{file, line, match, suggested_fix}]
  python_files: [list]
  js_ts_files: [list]
```

Display a compact audit summary to the user:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  REPO AUDIT: [REPO_PATH]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Root files found:    [list]
  Missing root files:  [list or "none"]
  Existing folders:    [list]

  Secrets found:       [count] — will move to .env
  Hardcoded paths:     [count] — will make agnostic
  Large model files:   [count and filenames, or "none"] — should use Git LFS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Step 3 — Ask for confirmation before changes

Use AskUserQuestion:

**Question:** "I've audited the repo. Ready to apply all fixes? (Secrets → .env, folder structure, missing files, agnostic paths)"

**Options:**
1. "Yes — apply all fixes now" — proceed with all steps below
2. "Show me the full change plan first" — print detailed plan, then ask again
3. "Cancel — I'll do it manually" — stop, print the audit report and exit

If user selects option 2, print:

```
CHANGE PLAN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FOLDERS TO CREATE:
  [list each missing folder — data/, models/, frontend/, backend/ — only those relevant to the project]

ROOT FILES TO CREATE:
  [list each missing file — README.md, PRD.md, requirements.txt, .env, .gitignore]

SECRETS TO EXTRACT:
  [for each secret: file → .env variable name]

PATHS TO MAKE AGNOSTIC:
  [for each hardcoded path: file:line → replacement]

.GITIGNORE ENTRIES TO ADD:
  .env (if not already present)
  [any other missing standard entries]

NO GIT ACTIONS — you handle commits and pushes.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Then ask again before proceeding.

---

## Step 4 — Create missing folders

Only create folders that make sense for this project. Use judgment:
- **data/** — create if the repo has any CSV, JSON, JSONL, Parquet, Excel, or SQLite files outside a designated folder
- **models/** — create if there are `.pkl`, `.pt`, `.pth`, `.onnx`, `.h5`, `.bin`, `.safetensors`, or model checkpoint files. Any model file over ~50MB should be tracked with Git LFS, not committed directly — flag these in the report.
- **frontend/** — create if there are React/Vue/HTML/CSS/JS files that clearly belong to a UI layer
- **backend/** — create if there are API/server files (FastAPI, Flask, Express, Django) that clearly belong to a server layer

For each folder to create, write a `.gitkeep` file so git tracks it:
```
[REPO_PATH]/data/.gitkeep
[REPO_PATH]/models/.gitkeep
[REPO_PATH]/frontend/.gitkeep
[REPO_PATH]/backend/.gitkeep
```

Move any files that clearly belong in these folders. When moving files:
- Data files (CSV, JSON datasets, Parquet, SQLite) → `data/`
- Model files (pkl, pt, onnx, h5, safetensors, bin weights) → `models/`
- Frontend files (React components, HTML templates, CSS, static assets) → `frontend/`
- Backend files (API routes, server entrypoints, middleware) → `backend/`

**Do not move** files that are ambiguous or at the project root for a clear reason (e.g., `app.py` as the main entrypoint stays at root).

Report every file move:
```
MOVED: src/data/train.csv → data/train.csv
MOVED: saved_models/model.pkl → models/model.pkl
```

---

## Step 5 — Extract secrets to .env

For every secret found in Step 2:

1. Determine the correct env var name in SCREAMING_SNAKE_CASE (e.g., `OPENAI_API_KEY`, `DATABASE_URL`, `STRIPE_SECRET_KEY`)
2. Read the `.env` file (or prepare to create it)
3. Add the entry: `ENV_VAR_NAME=<actual-value-found>`
4. In the source file, replace the hardcoded value with the env var reference:
   - Python: `os.environ.get("ENV_VAR_NAME")` or `os.getenv("ENV_VAR_NAME")`
   - JS/TS: `process.env.ENV_VAR_NAME`
   - Add the necessary import if missing:
     - Python: ensure `import os` is at the top; if `python-dotenv` patterns are present, ensure `from dotenv import load_dotenv` and `load_dotenv()` are present
     - JS/TS: note in the report that `dotenv` should be configured

Write the updated `.env` file with all extracted secrets plus any that already existed.

Create `.env.example` (safe to commit) with the same keys but empty values:
```
OPENAI_API_KEY=
DATABASE_URL=
STRIPE_SECRET_KEY=
```

Report every extraction:
```
EXTRACTED: config.py:14  OPENAI_API_KEY="sk-abc..."  →  os.environ.get("OPENAI_API_KEY")
EXTRACTED: db.py:8       DATABASE_URL="postgresql://..."  →  os.environ.get("DATABASE_URL")
```

---

## Step 6 — Make paths agnostic

For every hardcoded path found in Step 2:

**Absolute Windows paths** (e.g., `C:\Users\KRITIK\Projects\data`):
- Replace with a relative path from the project root (e.g., `./data`) or an environment variable reference
- If the path is a data directory, replace with `Path(__file__).parent / "data"` (Python) or `path.join(__dirname, "data")` (JS)
- If the path is a config or output path, add it as an env var: `DATA_DIR=./data`

**Absolute Unix paths** (e.g., `/home/ubuntu/project/`):
- Replace with relative paths or env vars

**Localhost URLs with hardcoded ports** (e.g., `http://localhost:8000`):
- Add to `.env`: `API_BASE_URL=http://localhost:8000`
- Replace in code with env var reference

**Hardcoded IP addresses** used as service endpoints:
- Add to `.env`: `SERVICE_HOST=192.168.x.x`
- Replace in code with env var reference

Report every change:
```
AGNOSTIC: utils.py:22  "C:\Users\KRITIK\data"  →  Path(__file__).parent / "data"
AGNOSTIC: client.py:5  "http://localhost:8000"  →  os.environ.get("API_BASE_URL")
```

---

## Step 7 — Create or update .gitignore

Read existing `.gitignore` (if any). Add any missing entries from the standard set below. Never remove existing entries.

Standard entries to ensure are present:

```gitignore
# Environment & secrets
.env
.env.local
.env.*.local

# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
dist/
*.egg-info/
.eggs/
*.egg
.venv/
venv/
env/
ENV/
.pytest_cache/
.mypy_cache/
.ruff_cache/
htmlcov/
.coverage
*.cover

# Data & models (large files — commit only samples or via Git LFS)
data/*.csv
data/*.parquet
data/*.db
data/*.sqlite
models/*.pkl
models/*.pt
models/*.pth
models/*.onnx
models/*.h5
models/*.bin
models/*.safetensors

# Node / JS
node_modules/
.npm
dist/
.next/
.nuxt/
*.log
npm-debug.log*

# Claude Code (machine-specific config — never commit)
.claude/
CLAUDE.md

# IDEs
.vscode/
.idea/
*.swp
*.swo
.DS_Store
Thumbs.db

# Jupyter
.ipynb_checkpoints/
*.ipynb_checkpoints

# OS
Thumbs.db
.DS_Store
```

Write the final `.gitignore`.

Report:
```
GITIGNORE: Added 12 entries (.env, __pycache__/, node_modules/, ...)
```

---

## Step 8 — Create or update requirements.txt

If `requirements.txt` does not exist:
- Scan all Python files for `import` and `from X import` statements
- List all third-party packages (exclude stdlib)
- Write `requirements.txt` with those packages (unpinned — let the user pin versions)
- If `python-dotenv` is not already imported but `.env` exists, add `python-dotenv` to requirements

If `requirements.txt` already exists:
- Check if `python-dotenv` is missing while `.env` is being used — add it if so
- Otherwise leave it unchanged

Report:
```
REQUIREMENTS: Created requirements.txt with [N] packages
```
or
```
REQUIREMENTS: Already exists — added python-dotenv
```

---

## Step 9 — Create or update README.md

The README must be **detailed and information-rich**. Someone who has never seen this project should be able to fully understand what it does, why it exists, how it is structured, and how to run it — purely from reading the README. Do not write a minimal stub. Read the actual code files to extract real, accurate detail before writing.

If `README.md` is **missing**, create a full one using this structure:

```markdown
# [Project Name]

> [One clear sentence: what the project does and who it is for.]

## What This Project Does

[3–5 sentences. Explain the problem it solves, how it solves it, and what the output or outcome is.
Be specific — use the actual names of files, models, APIs, or workflows found in the code.
A reader should finish this section knowing exactly what the project does without reading any code.]

## How It Works

[Describe the core workflow step by step. For example:
1. Input: what data or request goes in
2. Processing: what the code does with it (which modules, models, or APIs are involved)
3. Output: what comes out and where it goes

If there are multiple distinct flows (e.g. a training pipeline and an inference pipeline), describe each separately.]

## Project Structure

```
[project-name]/
├── backend/            # [describe what lives here — e.g. FastAPI routes, business logic]
│   ├── [key file]      # [what it does]
│   └── [key file]      # [what it does]
├── frontend/           # [describe — e.g. React UI, HTML templates]
│   └── [key file]      # [what it does]
├── data/               # [describe — e.g. training datasets, raw inputs, processed outputs]
├── models/             # [describe — e.g. saved model weights, checkpoints]
├── [other key file]    # [what it does]
├── .env.example        # Environment variable template — copy to .env and fill in values
├── requirements.txt    # Python dependencies
└── README.md
```

[After the tree, write 1–2 sentences for each major folder or file explaining its role.
Do not leave any folder as unexplained.]

## Setup

### Prerequisites
- [List every prerequisite: Python version, Node version, Docker, specific CLI tools, etc.]

### Installation

```bash
# Clone the repository
git clone <repo-url>
cd [project-name]

# Install dependencies
pip install -r requirements.txt   # Python
# or: npm install                 # Node

# Configure environment
cp .env.example .env
# Open .env and fill in all required values (see Environment Variables below)
```

### Running the Project

```bash
# [Primary run command with a comment explaining what it starts]
python main.py

# [Any secondary commands — e.g. dev server, worker, separate frontend]
npm run dev
```

[If there are multiple ways to run the project (CLI vs. server vs. notebook), document each one.]

## Environment Variables

All secrets and configuration live in `.env`. Never commit this file.

| Variable | Required | Description |
|----------|----------|-------------|
| [VAR_NAME] | Yes | [What it is, where to get it] |
| [VAR_NAME] | Yes | [What it is, where to get it] |
| [VAR_NAME] | No | [What it controls, default behaviour if unset] |

Copy `.env.example` to `.env` and fill in your values.

## Key Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| [package] | [version or "latest"] | [Why it is used — one phrase] |
| [package] | [version or "latest"] | [Why it is used] |

[Only list packages that are central to the project — skip obvious stdlib or utility packages.]

## Known Limitations / Notes

[List any important caveats, known issues, or things a new user is likely to trip over.
For example: "Requires GPU for inference", "Rate-limited to 60 requests/min", "Data must be in UTF-8 CSV format".]

```

If `README.md` **already exists**:
- Read the entire file first
- Identify which of the sections above are missing or underdeveloped (less than 2 sentences, placeholder text, or generic boilerplate)
- Expand or add those sections using real detail extracted from the code
- Do not delete or overwrite sections that are already well-written

Report:
```
README: Created  (or "Updated — expanded [section names]")
```

---

## Step 10 — Create PRD.md if missing

If `PRD.md` does **not** exist, create a minimal skeleton:

```markdown
# Product Requirements Document

**Project:** [Project Name]
**Version:** 0.1
**Status:** Draft
**Date:** [today's date]

---

## 1. Problem Statement

> TODO: Describe the problem this project solves.

---

## 2. Solution Overview

> TODO: Describe what this project does at a high level.

---

## 3. Core Features

- [ ] Feature 1
- [ ] Feature 2
- [ ] Feature 3

---

## 4. Out of Scope (v1)

- [ ] TODO

---

## 5. Open Questions

- [ ] TODO
```

If `PRD.md` already exists, leave it untouched.

Report:
```
PRD: Created skeleton PRD.md — fill in the TODOs
```
or
```
PRD: Already exists — not modified
```

---

## Step 11 — Set GitHub repository description and topics

Check whether the repo has a GitHub remote:

```powershell
git -C "[REPO_PATH]" remote -v
```

If no remote is found, skip this step and note it in the final report.

If a GitHub remote exists:

1. Check the current description and topics:
   ```powershell
   gh repo view --json description,repositoryTopics
   ```

2. Ask the user to provide or confirm:

   Use AskUserQuestion:
   - **Question 1:** "What should the GitHub repository description be? (1–2 sentences, shown on the repo homepage)"
     - Pre-fill with the current description if one exists, otherwise leave blank for the user to type.
   - **Question 2:** "What topics/tags should be set on this repo? (comma-separated, lowercase, e.g. `python, fastapi, machine-learning`)"
     - Pre-fill with any existing topics if present.

   If the user leaves both blank, skip this step.

3. Apply the changes using the GitHub CLI:

   ```powershell
   # Set description
   gh repo edit --description "[user-provided description]"

   # Set topics (replace all existing topics)
   gh repo edit --add-topic [topic1] --add-topic [topic2] ...
   ```

   To replace topics cleanly, first remove all existing topics then add the new ones:
   ```powershell
   # Get existing topics and remove each
   $topics = (gh repo view --json repositoryTopics | ConvertFrom-Json).repositoryTopics.name
   foreach ($t in $topics) { gh repo edit --remove-topic $t }

   # Add new topics
   foreach ($t in "[user-topics-split-by-comma]") { gh repo edit --add-topic $t.Trim() }
   ```

   Topics must be lowercase, use hyphens not spaces (e.g. `machine-learning` not `machine learning`). Convert any user-provided topics automatically.

4. Confirm the changes were applied:
   ```powershell
   gh repo view --json description,repositoryTopics
   ```

Report:
```
GITHUB METADATA:
  Description set: "[description]"
  Topics set:      [topic1], [topic2], [topic3]
```

---

## Step 12 — Final report

Print a complete summary of every change made:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  REPO ORGANISED: [REPO_PATH]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

FOLDERS CREATED:
  [list, or "none needed"]

FILES MOVED:
  [list each move, or "none"]

SECRETS EXTRACTED TO .env:
  [list each: file:line → ENV_VAR_NAME]

PATHS MADE AGNOSTIC:
  [list each: file:line → replacement]

FILES CREATED:
  [list: README.md / PRD.md / requirements.txt / .env / .env.example / .gitignore]

FILES UPDATED:
  [list: .gitignore (N entries added) / README.md (sections added) / etc.]

GITHUB METADATA:
  [Description set / not changed / skipped — no remote]
  [Topics set: list / not changed / skipped — no remote]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  NEXT STEPS (you handle these):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  1. Review .env — confirm all extracted values are correct
  2. Review .env.example — confirm no real secrets leaked in
  3. Review moved files — confirm nothing landed in the wrong folder
  4. Fill in TODOs in README.md and PRD.md
  5. When satisfied, commit and push:
       git add .
       git commit -m "chore: reorganise repo structure and clean up secrets"
       git push

  ⚠  DO NOT commit .env — it is in .gitignore for a reason.

  ⚠  LARGE MODEL FILES — use Git LFS:
     Any model file over ~50MB should not be committed directly.
     Set up Git LFS before committing:
       git lfs install
       git lfs track "models/*.pkl" "models/*.pt" "models/*.pth"
       git lfs track "models/*.onnx" "models/*.h5" "models/*.bin"
       git lfs track "models/*.safetensors"
       git add .gitattributes
     Committing large binaries directly bloats the repo permanently and
     makes it slow to clone — Git LFS keeps the repo light.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Important rules (never break these)

- **Never run `git commit` or `git push`** — not even if something goes wrong. Leave all git operations to the user.
- **Never create or modify `.claude/` folders or anything inside them** — `.claude/` is Claude Code's local config (skills, settings, memory) and is machine-specific. Do not create it, write to it, or move files into it. Add `.claude/` to `.gitignore` instead.
- **Never delete files** — only move them. If something needs to be removed (e.g., a file with a hardcoded secret), move it and fix the secret; do not delete the file.
- **Never overwrite .env** if it already exists with real values — only append new entries.
- **Never touch files in `.git/`**.
- **Preserve all existing code logic** — only change secret values and hardcoded paths, not the surrounding logic.
- **If a file move would break an import**, note it in the report under "Manual fixes needed" — do not silently break the code.
