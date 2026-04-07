# Cochl Release Notes Agent

A Claude Code skill that automatically drafts release notes from GitHub PRs or a local file — formatted for the right audience, in Cochl's voice.

Supports four output formats: **docs**, **newsletter (launch)**, **newsletter (monthly)**, and **internal**.

> 한국어 README는 [README_ko.md](README_ko.md)를 참고하세요.

---

## Prerequisites

Before setting up, make sure you have:

1. **Claude Code** installed  
   → [claude.ai/code](https://claude.ai/code) or run `npm install -g @anthropic-ai/claude-code`

2. **GitHub CLI** installed and authenticated  
   
   macOS:
   ```bash
   brew install gh
   ```
   Linux:
   ```bash
   sudo apt update && sudo apt upgrade -y gh
   ```
   Then log in to GitHub:
   ```bash
   gh auth login
   ```
   → See [gh auth login documentation](https://cli.github.com/manual/gh_auth_login) for step-by-step instructions.

   Required only if you use `--repo` to fetch PRs from GitHub.

---

## Installation

Copy the skill file into your Claude Code commands directory:

```bash
mkdir -p ~/.claude/commands
curl -o ~/.claude/commands/draft-release-notes.md \
  https://raw.githubusercontent.com/meanmin/release-note-agent/main/draft-release-notes.md
```

That's it. Claude Code picks up skills from `~/.claude/commands/` automatically — no restart needed.

---

## Usage

Open Claude Code and run the command with the arguments below.

### From GitHub PRs (`--repo`)

The skill fetches PRs from the specified repository and uses them as source material.

```
# Generate docs release notes from a GitHub repo
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience docs

# Generate an internal changelog
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience internal

# Generate a newsletter announcement (new feature launch)
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience newsletter --style launch

# Generate a newsletter update (monthly digest)
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience newsletter --style monthly
```

The skill checks open PRs first. If none are found, it retries with merged PRs automatically.

### From a local file (`--file`)

Pass any PDF, TXT, or Markdown file as the source instead of GitHub PRs.

```
/draft-release-notes --file ~/Downloads/update.pdf --version v1.7.0 --audience newsletter --style launch
```

Claude Code reads the file directly — no conversion needed.

### Arguments

| Argument | Required | Description |
|---|---|---|
| `--repo` | One of `--repo` or `--file` | GitHub repo to pull PRs from (e.g. `cochlearai/sense-sdk`) |
| `--file` | One of `--repo` or `--file` | Local file path (PDF, TXT, or Markdown) |
| `--version` | Yes | Release version (e.g. `v1.7.0`) |
| `--audience` | Yes | `docs`, `newsletter`, or `internal` |
| `--style` | Only for newsletter | `launch` or `monthly` — see below |

---

## GitHub Draft Release

When using `--repo` and `--audience docs`, the skill will ask whether you want to create a **GitHub draft release** automatically after generating the notes.

If you confirm, it runs:
```bash
gh release create <version> --repo <owner/repo> --draft --title "<version>" --notes-file <output-file>
```

The draft will appear under **Releases → Draft** in your GitHub repo. You can review and publish it when ready.

> Note: `gh` must be authenticated and you must have write access to the target repo.

---

## Output formats

### `--audience docs`
Short and scannable. Written for the Cochl documentation site. No marketing language — this is a reference document.

**Example:**
```
April 7, 2026
Cochl.Sense v1.7.0 released

• Added adaptive audio activity filtering — skips inference on silent frames to reduce CPU usage on edge devices; controlled via a single `activity_sensitivity` parameter
• Added Automatic Gain Control (AGC) — normalizes mic input levels across environments; controlled via `target_mav`
```

---

### `--audience newsletter --style launch`
A dedicated feature announcement. Written like Cochl's Custom Sound launch newsletter — benefit-first, empowering, with a "How it works" walkthrough.

Best for: introducing a brand-new capability for the first time.

**Example:**
```
Run Cochl.Sense smarter — not harder.

• Lightweight: No buffer required — adapts on the fly with minimal overhead
• Adaptive: Adjusts automatically based on the last 10 minutes of audio trends
• Simple to use: One parameter per module — integration takes minutes

How it works
01 Drop the module in front of your inference pipeline
02 Set your sensitivity or gain target with a single parameter
03 Let it adapt automatically

Try it now → [GitHub]
```

---

### `--audience newsletter --style monthly`
A concise update section for the monthly Cochl Bites newsletter. Written for developers — technical enough to be useful, short enough to fit in a digest.

Best for: regular version updates.

**Example:**
```
Cochl.Sense Audio Preprocessing Module — now available

We've released a new open-source utility library with two modules for edge and streaming deployments.

Key changes:
- Adaptive Audio Activity Filtering: skips inference on low-activity frames; parameter: `activity_sensitivity`
- Automatic Gain Control: normalizes gain without buffering; parameter: `target_mav`

Refer to the GitHub repo for integration examples.
```

---

### `--audience internal`
Full technical changelog for the engineering team. Includes PR references, root causes, a migration checklist, and a separate "Pending / Not yet shipped" section.

**Example:**
```
## v1.7.0 Internal Release Notes — April 7, 2026

### Features
- **Adaptive Audio Activity Filtering**: Buffer-free pre-filter using rolling mean amplitude over 600 samples (~10 min). Exposes `activity_sensitivity` parameter.
- **Automatic Gain Control (AGC)**: Adaptive gain normalization, same 10-min rolling window. Exposes `target_mav`.

### Breaking Changes & Migration Checklist
- [ ] No breaking changes — standalone opt-in module

### Pending / Not yet shipped
- Tag Hierarchy auto-propagation — hardcoded in Sense API, cleanup and docs still pending
```

---

## Output files

Generated files are saved to `~/release-notes/` with this naming convention:

| Source | Filename |
|---|---|
| `--repo cochlearai/sense-sdk` | `~/release-notes/sense-sdk-v1.7.0-docs.md` |
| `--repo` + newsletter | `~/release-notes/sense-sdk-v1.7.0-newsletter-launch.md` |
| `--file update.pdf` + internal | `~/release-notes/update-internal.md` |

---

## Tips

- If you use `--file` with a PDF, Claude Code reads the file directly — no conversion needed.
- When pulling from `--repo`, the skill checks open PRs first. If none are found, it retries with merged PRs.
- If your source mixes shipped features with planned items (roadmap, backlog), the agent will flag planned items separately in the output — it won't include them as released.
- The skill follows Cochl's tone & manner for all audience types. No "We are excited to announce."

---

## Beta feedback

This skill is in beta. If something looks off — wrong tone, missing info, confusing output — please share your feedback:

- Open an issue in this repo
- Or contact the maintainer directly at **msjang@cochl.ai**

We're iterating on this based on real usage. Your examples and edge cases help a lot.
