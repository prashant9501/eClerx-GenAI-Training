# Git Workflow — Participant Reference

This is your reference for accessing the training content for the **eClerx AI/GenAI Engineer Training Program**. Your trainer (Prashant) uploads day-wise content to GitHub as the program progresses. You will use Git to download the content and stay synced as new modules and updates are pushed.

**Repo:** `https://github.com/prashant9501/eClerx-GenAI-Training`

> The repo's `README.md` is the **canonical reference for environment setup, dependencies, API keys, and lab conventions**. This document covers the Git side of the workflow — how to get the content onto your machine and stay in sync as it grows.

---

## 1. What's in the Repo

The repo is organised by **module** (each module = one training day). Each module folder contains:

- **Jupyter notebooks** (`.ipynb`) — the hands-on labs (~1.5 hours of trainee time each)
- **Reveal.js HTML decks** (`.html`) — concept slides for the day. No install needed; just open in any browser

**Live now:** Modules 1, 2, 3 (Days 1–3)
**Coming through the program:** Modules 4–10 will be added as the program progresses

The folder names use the actual module titles, e.g.:
```
Module 1 - Python for GenAI & LLM Foundations/
Module 2 - Prompt Engineering with LangChain/
Module 3 - LangChain Deep Dive/
```

---

## 2. One-Time Setup (Day 0 — Before the Training Begins)

### 2.1 Install Git

| OS | How |
|---|---|
| **Windows** | Download from `https://git-scm.com/download/win` and run the installer with default options |
| **macOS** | `git --version` — if missing, you'll be prompted to install developer tools. Or `brew install git` |
| **Linux** | `sudo apt install git` (Ubuntu/Debian) or `sudo dnf install git` (Fedora/RHEL) |

Verify:
```bash
git --version
```

### 2.2 Configure your name and email (one-time)

```bash
git config --global user.name "Your Full Name"
git config --global user.email "your-email@example.com"
```
This is purely for any local commits if you choose to track your own experiments. You will not push anything back to the trainer's repo.

### 2.3 Install VS Code (the IDE you'll use during training)

Download from `https://code.visualstudio.com/` and install these extensions (Extensions panel on left → search and install):
- **Python** (by Microsoft)
- **Jupyter** (by Microsoft)
- **GitLens** (helpful for seeing commit history)

### 2.4 Clone the repo

Pick a folder where the training content will live. For example:

```bash
# macOS / Linux
mkdir -p ~/work && cd ~/work

# Windows (PowerShell)
mkdir C:\work; cd C:\work
```

Then clone:
```bash
git clone https://github.com/prashant9501/eClerx-GenAI-Training.git
cd eClerx-GenAI-Training
```

You'll see the module folders for whatever has been published so far (Modules 1–3 at the time of writing).

### 2.5 Open in VS Code

```bash
code .
```

The repo opens as your VS Code workspace.

### 2.6 Set up Python environment, dependencies, API keys

**Follow the "Running the labs" section in the repo's `README.md`.** It is the canonical source for:
- Creating the `.venv` virtual environment
- Installing the package baseline (`langchain>=1.0`, `langchain-core>=1.0`, `openai`, `anthropic`, etc.)
- Configuring the `.env` file with your API keys (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `AWS_*`, etc.)
- Launching Jupyter Lab

The `.env` file is git-ignored — your keys never leave your machine.

### 2.7 Tell VS Code to use the venv kernel

1. Open any `.ipynb` notebook in VS Code (e.g., `Module 1 - Python for GenAI & LLM Foundations/Lab_1.1_Hello_LLM_Client.ipynb`)
2. Top-right of the notebook → click "Select Kernel" → "Python Environments" → pick `.venv`
3. VS Code will use this kernel for all subsequent notebook runs

---

## 3. Daily Workflow — Getting the Latest Content

The trainer will push new content to GitHub throughout the program: new modules will appear, and existing labs/decks may be updated mid-session if a fix is needed.

**To get the latest version, run a single command:**

```bash
cd ~/work/eClerx-GenAI-Training       # be inside the cloned folder
git pull origin main
```

That's it. Any new files appear and any updated files refresh.

### 3.1 When to pull

- **At the start of each training day** — get whatever the trainer has uploaded for that day
- **If the trainer says** "I just pushed an update for Lab 2.4" — pull to grab it
- **Before each session** if you want to be sure you have the freshest version
- **Whenever a new module appears** (e.g., Module 4 lands on Day 4) — pull to get the new folder

### 3.2 What `git pull` does and doesn't do

✅ **It WILL:**
- Add new files (e.g., new module folders, new labs)
- Update files the trainer changed — **as long as you haven't modified those exact files locally**

⚠️ **It WILL warn you** about a "merge conflict" if both you and the trainer changed the same file. To avoid this, follow the safe-editing pattern in §4.

❌ **It will NOT:**
- Touch your `.env` file (git-ignored)
- Touch your `.venv` folder (git-ignored)
- Touch any `my_*.ipynb` copies you make (recommended pattern below)
- Delete files you've added that the trainer doesn't have

---

## 4. Safe Editing Pattern (Recommended)

When you want to experiment with a notebook (run cells, scribble in markdown, modify code), **make a personal copy first**. The trainer will keep updating the original file, and you don't want your experiments to clash with the trainer's next push.

```bash
# Inside Module 2 - Prompt Engineering with LangChain/
cp Lab_2.4_Output_Parsers_DeepDive.ipynb my_Lab_2.4_Output_Parsers_DeepDive.ipynb
```

Or in VS Code: right-click the file → Copy → Paste, then rename to add the `my_` prefix.

Edit the `my_*` copy. The original stays clean and pulls cleanly when the trainer updates it.

`my_*` files are not git-tracked unless you add them — the trainer's `.gitignore` should keep them out of version control. Even if not, they live only on your machine.

---

## 5. Common Situations

### 5.1 "I edited a lab notebook directly and now `git pull` says there's a conflict"

You ran cells in the original `Lab_X.Y_*.ipynb` (which changes its execution counts and outputs), and the trainer also pushed an update to the same file. Two options:

**Option A — Discard your changes, take the trainer's version (most common):**
```bash
git checkout -- "Module 3 - LangChain Deep Dive/Lab_3.1_LangChain_Fundamentals.ipynb"
git pull origin main
```

**Option B — Keep your version safe, then take the trainer's:**
```bash
# Save your version as a my_* copy first
cp "Module 3 - LangChain Deep Dive/Lab_3.1_LangChain_Fundamentals.ipynb" \
   "Module 3 - LangChain Deep Dive/my_Lab_3.1_LangChain_Fundamentals.ipynb"
git checkout -- "Module 3 - LangChain Deep Dive/Lab_3.1_LangChain_Fundamentals.ipynb"
git pull origin main
```

### 5.2 "I want to throw away ALL my local changes and start fresh"

```bash
cd ~/work/eClerx-GenAI-Training
git fetch origin
git reset --hard origin/main
```

⚠️ This wipes any tracked-file edits you've made. It does NOT delete:
- Your `.env` file (git-ignored)
- Your `.venv` folder (git-ignored)
- Your `my_*.ipynb` copies (untracked)

### 5.3 "How do I see what just got pulled?"

After running `git pull`:
```bash
git log --oneline -10        # last 10 commits with subject lines
```

Or use VS Code's **Source Control** panel (Ctrl+Shift+G / Cmd+Shift+G) — it shows commit history visually with the GitLens extension.

### 5.4 "A new module appeared after pulling — how do I run its labs?"

1. Open the new folder in VS Code's file explorer
2. Open any `.ipynb` inside it
3. Make sure the `.venv` kernel is selected (top-right of notebook)
4. The first cell of every lab is a `pip install` — run it once to install any new packages that lab needs that weren't in your baseline install

### 5.5 "I want to view the slide deck for a module"

The HTML decks (e.g., `Module_2_Prompt_and_Context_Engineering.html`) are self-contained — just double-click the file or open it in your browser.

Navigation:
- **←/→ arrow keys** — next/previous slide
- **F** — fullscreen
- **ESC** — overview mode (see all slides)
- Append `?print-pdf` to the URL for PDF export

---

## 6. Workflow Summary (Print This)

```
TO START EACH DAY:
   cd ~/work/eClerx-GenAI-Training
   git pull origin main
   code .                        (opens VS Code in the repo folder)

TO RUN A NOTEBOOK:
   Open the .ipynb file in VS Code
   Make sure ".venv" kernel is selected (top-right of notebook)
   Run cells with Shift+Enter

WHEN TRAINER ANNOUNCES AN UPDATE:
   cd ~/work/eClerx-GenAI-Training
   git pull origin main          (instantly gets the new content)

TO EXPERIMENT WITH A LAB SAFELY:
   Copy it to my_<filename>.ipynb first
   Edit the copy, leave the original clean
   The original will pull cleanly when the trainer updates it
```

---

## 7. Troubleshooting

| Problem | Solution |
|---|---|
| `command not found: git` | Git not installed. Go back to §2.1 |
| `Permission denied (publickey)` | You used the SSH URL but didn't set up SSH keys. Use the HTTPS URL: `git clone https://github.com/prashant9501/eClerx-GenAI-Training.git` |
| `Your local changes would be overwritten by merge` | You have uncommitted edits that conflict. See §5.1 |
| `fatal: not a git repository` | You're not inside the cloned folder. Run `cd ~/work/eClerx-GenAI-Training` first |
| Notebook kernel error / `ModuleNotFoundError` | Confirm `.venv` is activated AND selected as the kernel. Re-run the first `pip install` cell of the notebook |
| `git pull` says "Already up to date" but you don't see the file | The trainer hasn't pushed yet, OR you're in the wrong folder. Verify with `pwd` and ask the trainer |

---

## 8. Quick Reference Card

| Task | Command |
|---|---|
| Get the latest content | `git pull origin main` |
| Check what's changed locally | `git status` |
| See recent updates | `git log --oneline -10` |
| Discard a single file's edits | `git checkout -- "<filename>"` |
| Discard ALL local edits | `git fetch origin && git reset --hard origin/main` |
| Open repo in VS Code | `code .` (from inside the repo folder) |
| Activate Python venv (Mac/Linux) | `source .venv/bin/activate` |
| Activate Python venv (Windows PowerShell) | `.venv\Scripts\Activate.ps1` |
| Activate Python venv (Windows Git Bash) | `source .venv/Scripts/activate` |

---

**Need help?** Ask in the training Slack/Teams channel or message the trainer directly.

**Important:** The repo's `README.md` is the source of truth for setup, dependencies, and lab conventions. This document only covers the Git workflow.
