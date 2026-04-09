# Workshop Prerequisites & Installation Guide

> Complete setup guide for participants of the Claude Code Dynamic Workshop.
> Please complete these steps **before** the workshop begins.

---

## System Requirements

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| **OS** | Windows 10, macOS 12, Ubuntu 20.04 | Latest version |
| **Node.js** | v18.0+ | v20 LTS or v22 LTS |
| **RAM** | 4 GB | 8 GB+ |
| **Disk** | 500 MB free | 2 GB+ |
| **Terminal** | Any modern terminal | Windows Terminal, iTerm2, or Warp |
| **Internet** | Required | Stable connection (API calls) |

---

## Step 1: Install Node.js

Claude Code requires Node.js 18 or higher.

**Check if installed:**
```bash
node --version   # Should show v18+ or v20+
npm --version    # Should show 9+
```

**Install if needed:**
- **Windows:** Download from [nodejs.org](https://nodejs.org/) (LTS version) or use `winget install OpenJS.NodeJS.LTS`
- **macOS:** `brew install node` or download from [nodejs.org](https://nodejs.org/)
- **Linux:** `curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash - && sudo apt-get install -y nodejs`

---

## Step 2: Install Claude Code

```bash
# Install globally
npm install -g @anthropic-ai/claude-code

# Verify installation
claude --version
```

**Alternative — without global install:**
```bash
npx @anthropic-ai/claude-code
```

**Desktop App (optional):**
- Download from [claude.ai/code](https://claude.ai/code) for Mac or Windows
- Web version also available at the same URL

---

## Step 3: Authenticate

```bash
# Start Claude Code and log in
claude
# Then run:
/login
```

You need one of:
- **Claude Pro/Max subscription** (recommended for workshop)
- **Anthropic API key** (set `ANTHROPIC_API_KEY` environment variable)
- **AWS Bedrock** or **Google Vertex** credentials (enterprise setups)

**Verify authentication works:**
```bash
claude --print "Say hello"
# Should return a response without errors
```

---

## Step 4: Install Git

Required for demo repository and version control exercises.

**Check if installed:**
```bash
git --version   # Should show 2.30+
```

**Install if needed:**
- **Windows:** `winget install Git.Git` or download from [git-scm.com](https://git-scm.com/)
- **macOS:** `xcode-select --install` or `brew install git`
- **Linux:** `sudo apt install git`

---

## Step 5: Install Python 3 (for Block 2+)

Required for the workshop-playground demo repo (pytest tests, vulnerability demos).

**Check if installed:**
```bash
python3 --version   # Should show 3.9+
pip3 --version
```

**Install if needed:**
- **Windows:** `winget install Python.Python.3.12` or download from [python.org](https://python.org/)
- **macOS:** `brew install python`
- **Linux:** `sudo apt install python3 python3-pip`

---

## Step 6: Install GitHub CLI (optional, for Block 3)

Used in advanced exercises for PR workflows.

```bash
# Windows
winget install GitHub.cli

# macOS
brew install gh

# Linux
sudo apt install gh

# Then authenticate
gh auth login
```

---

## Step 7: Clone the Workshop Repository

```bash
# Navigate to your workspace
cd ~/Desktop

# Clone the workshop playground (demo repo with intentional vulnerabilities)
git clone <workshop-playground-url>
cd workshop-playground

# Install Python dependencies
pip3 install -r requirements.txt

# Verify tests run
python3 -m pytest -v
```

---

## Pre-Workshop Checklist

Run through this checklist to make sure everything works:

- [ ] `node --version` shows v18+
- [ ] `claude --version` shows current version
- [ ] `claude --print "Hello"` returns a response (authentication works)
- [ ] `git --version` shows 2.30+
- [ ] `python3 --version` shows 3.9+
- [ ] Workshop playground repo cloned and tests pass
- [ ] Terminal supports Unicode and ANSI colors (try `echo -e "\033[32mGreen\033[0m"`)

---

## Quick Diagnostic

If something isn't working, run the built-in doctor:

```bash
claude /doctor
```

This checks your environment and reports issues.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `npm: command not found` | Install Node.js first (Step 1) |
| `claude: command not found` | Run `npm install -g @anthropic-ai/claude-code` again, check PATH |
| Authentication fails | Check API key or subscription status, try `/login` again |
| `EACCES` permission error | Use `npm config set prefix ~/.npm-global` and add to PATH |
| Python not found on Windows | Use `python` instead of `python3`, or install from Microsoft Store |
| Tests fail in playground | Check Python version, run `pip3 install -r requirements.txt` |
| Slow/no response | Check internet connection, try `claude --verbose` for details |

---

## What to Bring

- Laptop with the above setup completed
- Curiosity and questions
- A project idea you'd like to try with Claude Code (optional but fun)

---

**Questions?** Contact the workshop organizer before the session.

**Last Updated:** 2026-04-03 | **Workshop:** Claude Code Dynamic Workshop
