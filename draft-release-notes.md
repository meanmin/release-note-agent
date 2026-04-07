---
name: draft-release-notes
description: "Generate Cochl release notes from GitHub PRs or a local file. Usage: /draft-release-notes --repo <owner/repo> OR --file <path> --version <vX.X.X> --audience <docs|newsletter|internal> [--style <launch|monthly>]"
---

Generate Cochl.Sense release notes based on the provided source and audience.

## How to use

**From GitHub PRs:**
`/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience docs`

**From a local file (PDF, TXT, MD):**
`/draft-release-notes --file ~/Downloads/update.pdf --version v1.7.0 --audience newsletter --style launch`

### Arguments
- `--repo` : GitHub repo — fetches open or merged PRs
- `--file` : Local file path (PDF, TXT, or Markdown)
- `--version` : Release version (e.g. `v1.7.0`)
- `--audience` : `docs` | `newsletter` | `internal`
- `--style` : For newsletter only — `launch` (new feature announcement) or `monthly` (regular update digest)

If `--audience newsletter` is given without `--style`, ask: "Is this a new feature launch or a regular version update? (launch / monthly)"
All other arguments are required. If missing, ask before proceeding.

---

## Step 1: Load source content

**If `--repo` is given:**
```bash
gh pr list --repo <repo> --state open --json number,title,body,labels,url --limit 20
```
If no open PRs, retry with `--state merged`.

**If `--file` is given:**
Read the file using the Read tool and extract all relevant content.

---

## Step 2: Analyze content

Identify and categorize:
- **New features** (feat): New capabilities added
- **Improvements**: Performance, UX, or reliability enhancements
- **Bug fixes** (fix): Problems resolved
- **Breaking changes**: API or behavior changes requiring user action
- **Skip**: Internal refactoring, CI changes, typos, planning/roadmap items not yet shipped

If the source mixes shipped features with planned items, only include what is confirmed shipped.

---

## Step 3: Apply Cochl tone & manner

Before writing, internalize these principles — they apply to ALL audience types:

### Cochl brand voice
- **Precise but human**: Technically accurate, never cold. Cochl talks to engineers but sounds like a person.
- **Benefit-first**: Lead with what the customer gains, not what the team built.
- **Specific over vague**: Use real numbers and parameter names when available (e.g., "~12% accuracy improvement", "`activity_sensitivity` parameter").
- **Confident, not boastful**: State facts clearly. Don't oversell.
- **Empowering**: Especially in newsletters — make the reader feel capable, not overwhelmed.
- **Concise**: No filler phrases. Every sentence earns its place.

### What Cochl does NOT sound like
- ❌ "We are excited to announce..."
- ❌ "Leveraging cutting-edge AI technology..."
- ❌ "This groundbreaking update..."
- ✅ "Cochl.Sense v1.7.0 is here — with a smarter model and stable 24/7 performance."
- ✅ "Run Cochl.Sense smarter — not harder."
- ✅ "Define your own sound with just a small amount of data."

---

## Step 4: Write release notes by audience

### audience = `docs`
Style reference: https://docs.cochl.ai/sense/cochl.sense-edge-sdk/releasenote/

```
[Month DD, YYYY]
[Product Name vX.X.X] released

• [What changed and why it matters — 1 sentence, benefit-focused]
• [What changed and why it matters]

**Breaking Changes**
• [What changed and what the user must do] — See [migration guide](link)
```

Rules:
- Max 2 lines per bullet
- No marketing language — this is a reference document
- Breaking changes always in a separate section
- Link to docs or GitHub where relevant
- English only

---

### audience = `newsletter` + style = `launch`
Style reference: Cochl Custom Sound launch newsletter

```
**[Headline — what the customer can now DO, not the feature name]**

[1 sentence that adds context or quantifies the value]

• [Benefit label]: [What it means for the customer]
• [Benefit label]: [What it means for the customer]
• [Benefit label]: [What it means for the customer]

How it works
01 [Simple first action]
02 [Second step]
03 [Outcome the customer experiences]

[Warm closing line + CTA — e.g., "Try it now →" or "Read the docs →"]

---
⚠️ Important notice *(only if breaking change exists)*
[Plain-language explanation of what changed and what to do, with migration guide link]

We'd love to hear your feedback — support@cochl.ai
```

Rules:
- Headline = customer transformation, never the internal feature name
- 3 benefit pillars: simple, empowering language
- "How it works" makes complex features feel approachable
- Metrics make it credible — include if available
- English only

---

### audience = `newsletter` + style = `monthly`
Style reference: Cochl Bites Monthly SDK update sections

```
**[Product Name] [vX.X.X] update**

[1–2 sentences: what changed and why it matters to developers]

Key changes:
- [Specific change — include parameter names, version numbers if relevant]
- [Specific change]
- [Specific change]

⚠️ **Heads up**: [Breaking change — what changed, what to do]
Refer to [docs/GitHub link] for details.
```

Rules:
- Developer audience — technical terms, parameter names, and version numbers are welcome
- Tight and direct — no marketing language
- Breaking changes flagged clearly with ⚠️
- Always link to relevant docs or GitHub
- English only

---

### audience = `internal`

```
## [vX.X.X] Internal Release Notes — [Date]

### Features
- [PR #N or source ref] **[Title]**: [Technical explanation — what was built, how it works, key implementation detail]

### Bug Fixes
- [PR #N] **[Title]**: [Root cause + what was fixed]

### Breaking Changes & Migration Checklist
- [ ] [Specific action required — who does it, what they change]
- [ ] [Another action]

### Pending / Not yet shipped
[Items from the source that appear planned but not confirmed shipped]

### Notes
[Known issues, dependencies, or follow-up items]
```

Rules:
- Include PR numbers or source references for traceability
- Mention root causes for bug fixes
- Separate "pending" items clearly — don't confuse roadmap with shipped
- Migration checklist must be actionable
- English only

---

## Step 5: Save output

Save to:
- `--repo` source: `~/release-notes/[repo-name]-[version]-[audience]-[style].md`
- `--file` source: `~/release-notes/[filename-without-ext]-[audience]-[style].md`

Omit `-[style]` if audience is not newsletter. Create the directory if it doesn't exist.
Show the full content in chat and confirm the file path.

---

## Step 6: GitHub Draft Release (optional)

**Only applies when `--repo` is provided and `--audience docs`.**

After saving the file, ask the user:

> "GitHub draft release를 자동으로 생성할까요? (yes / no)"
> "Would you like to create a GitHub draft release automatically? (yes / no)"

If yes, run:
```bash
gh release create [version] --repo [owner/repo] --draft --title "[version]" --notes-file [saved-file-path]
```

Then confirm with the draft release URL returned by the command.

If the command fails (e.g. tag already exists, insufficient permissions), show the error message and suggest the user create the release manually on GitHub.
