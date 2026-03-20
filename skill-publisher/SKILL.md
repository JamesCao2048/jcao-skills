---
name: skill-publisher
description: Use when you want to prepare an internal Claude Code skill for public release. Scans a skill directory for privacy leaks, i18n issues, structure problems, and content clarity, then produces a sanitized publication-ready copy. Never modifies the source. Triggers on: "publish skill", "prepare skill for release", "skill publisher", "准备发布skill", "skill发布", "发布skill", "sanitize skill".
---

# Skill Publisher

Prepare an internal Claude Code skill for public release through a 4-category scan-and-approve workflow. Outputs a sanitized copy to a separate directory — the source is never modified.

## Step 0: Receive Input

Ask the user:
1. **Skill path** (required): path to the skill directory to publish
2. **Output path** (optional): where to write the publication-ready copy (default: `./publish_skills_output/`)

Output directory: `<output-path>/<skill-name>/`

Read all files in the skill directory before proceeding.

---

## Step 1: Scan & Build Change List

Run all checks across 4 categories. Collect the complete list before presenting anything.

### 🔒 Privacy

**Scope:** all files in the skill directory (SKILL.md, references/, scripts/, examples/, assets/)

Scan all files (including `scripts/`):

| Check | Detection | Proposed Action |
|-------|-----------|-----------------|
| Personal paths | `/Users/\w+/`, `/home/\w+/`, `~/`, `C:\Users\` | Replace with relative path or `<path>` placeholder |
| Internal domains | `\w+\.internal`, `\w+\.corp`, private IPs (10.x, 172.16-31.x, 192.168.x) | Replace with `example.com` or `<internal-host>` |
| Credentials | `api_key`, `token`, `password`, `secret` followed by `=` or `:` and a value that is not all-uppercase, not wrapped in `<>` or `[]`, and is longer than 8 characters | Replace value with `<YOUR_API_KEY>` etc. |
| Project/product names | Named projects or products that aren't the skill itself | Flag — ask: anonymize / replace / keep |
| People/team names | Person names or team identifiers in prose | Flag — ask: anonymize / remove / keep |

**scripts/ findings:** Listed here in 🔒 Privacy. Per finding, user chooses:
- **fix** — minimal sanitization (comments/strings only, no logic changes)
- **skip** — copy script as-is (marks output ⚠️)
- **exclude** — don't copy the script to output

### 🌐 i18n

**Scope:** SKILL.md and files in `references/` and `agents/` (instruction files Claude runs)

Scan `SKILL.md`, `references/`, and `agents/`:

| Check | Detection | Proposed Action |
|-------|-----------|-----------------|
| Non-English instructions | Non-ASCII paragraphs in body text | Translate to English (show draft) |
| Hardcoded language directives | e.g. "所有文字：中文", "回复用中文", "answer in Chinese" | Replace with `match the user's language` |
| Non-English trigger words | Non-ASCII text in frontmatter `description` | Keep originals + add English equivalents |
| Non-English placeholders | e.g. `[主题名称]`, `[项目名]` | Replace with `[topic-name]`, add original as comment |

### 📐 Structure

**Scope:** directory layout and SKILL.md (not the content of scripts/)

**File placement taxonomy** (per [anthropics/skills](https://github.com/anthropics/skills) convention):

| File type | Correct location |
|-----------|-----------------|
| Knowledge docs, schemas, best practices, fill-in templates Claude reads at runtime | `references/` |
| Sub-agent role/instruction files Claude spawns as separate agents | `agents/` |
| Executable scripts | `scripts/` |
| Example outputs and sample data | `examples/` |
| Static assets (images, etc.) | `assets/` |

**Checks:**

| Check | Detection | Proposed Action |
|-------|-----------|-----------------|
| Missing/invalid frontmatter | No `---` block at top, or missing `name` / `description` | Generate standard frontmatter |
| Wrong description style | `description` doesn't start with "Use when" | Rewrite to "Use when..." format |
| SKILL.md too long | Line count > 500 | Suggest splitting into `references/` by topic |
| Reference file too long | Any `references/` file > 300 lines | Suggest adding a table of contents |
| Misplaced files at skill root | `.md` files other than `SKILL.md` / `README.md` at skill root | Classify by content (see below), offer to move — user chooses yes/no |
| Misnamed subfolder | A subfolder exists whose name isn't one of: `references/`, `agents/`, `scripts/`, `examples/`, `assets/` | Classify by content (see below), suggest correct name — user chooses yes/no |
| Unrecognized files | After classification, files still not fitting any category | Flag — ask user: remove / keep as-is |

**Classification rules** (for misplaced/misnamed files — read the file content to decide):

- **→ `references/`**: knowledge docs, schemas, fill-in templates Claude renders into output, best-practice guides — anything Claude reads to inform its work but does not spawn separately
- **→ `agents/`**: files that define a distinct agent role (contain role persona + instructions for an agent Claude spawns, e.g. "You are an Analyzer. Your job is to...")
- **→ `scripts/`**: executable code (shell, Python, etc.) — check shebang or file extension
- When ambiguous, show the first 5 lines of the file and ask the user which category fits

### 📝 Content

**Scope:** SKILL.md frontmatter and body text

| Check | Detection | Proposed Action |
|-------|-----------|-----------------|
| Description too long | Frontmatter `description` > 500 characters | Suggest shorter version |
| Domain-specific examples in general skill | General-purpose skill + domain-specific examples | Suggest generic replacements |
| Missing pitfalls section | No `## Common Mistakes` or `## Pitfalls` section | Suggest adding one (optional) |
| Missing Input spec | No `## Input` section describing parameters | Suggest adding with inferred params |
| Missing Output spec | No `## Output` section | Suggest adding with inferred format |
| Missing checkpoints | Workflow has irreversible actions without `⛔ CHECKPOINT` | Suggest adding confirmation steps |

---

## Step 2: Present Change List for Approval

Present findings **one category at a time**: 🔒 Privacy → 🌐 i18n → 📐 Structure → 📝 Content.

For each category:

1. **Show all findings** in a numbered list:
   ```
   🔒 Privacy — 3 findings

   [1] scripts/deploy.sh:12 — Personal path: /Users/alice/projects/myapp
       Proposed: → <project-path>

   [2] SKILL.md:45 — Internal domain: api.corp.mycompany.com
       Proposed: → example.com

   [3] SKILL.md:8 — Project name: "MyInternalTool"
       Flag: private product name → anonymize / keep / replace with ___?
   ```

2. **Ask for approval** (choose one):
   - **Approve all** — apply all proposed changes
   - **Give feedback** — describe adjustments, regenerate this category
   - **Skip category** — copy as-is (Privacy skips mark output ⚠️)
   - **Handle individually** — respond to each item one by one

   When "Handle individually" is chosen, show each finding one at a time:
   ```
   [1/3] 🔒 Personal path in scripts/deploy.sh:12
         Proposed: /Users/alice/projects/myapp → <project-path>
         → approve this change / modify (describe what you want) / skip this item
   ```
   Wait for a response before showing the next item.

3. Wait for user response before moving to next category.

If a category has no findings: state "No findings — moving on." and continue automatically.

---

## Step 3: Execute Changes

After all categories are approved:

1. Create output directory: `<output-path>/<skill-name>/`
2. Copy all source files to output
3. Apply all approved changes to the copies
4. For `scripts/` files: apply fix / skip / exclude per user decision
5. Write any generated content (translations, new frontmatter, suggested sections)

Do **not** touch the source skill directory.

**Edge cases:**
- Output directory already exists: ask the user to confirm overwrite or supply a different path before proceeding.
- Source directory is empty or contains only unrecognized files: state the situation and stop — do not create an empty output directory.

---

## Step 4: Final Review

Print summary:

```
📦 Output: <output-path>/<skill-name>/

Files written:
  ✅ SKILL.md
  ✅ references/patterns.md
  ⚠️  scripts/deploy.sh  (copied as-is — unresolved path at line 12)

Changes applied:
  🔒 Privacy: 2 replacements, 1 skipped
  🌐 i18n: 3 translations applied
  📐 Structure: frontmatter rewritten
  📝 Content: 0 changes

Status: ⚠️ review-before-publish
Reason: 1 Privacy item skipped (scripts/deploy.sh:12)

Manual follow-up:
  - scripts/deploy.sh:12 — personal path still present, review before publishing
```

**Status rules:**
- `✅ publication-ready` — all Privacy items resolved (approved or empty); no unresolved scripts/ findings
- `⚠️ review-before-publish` — any Privacy item was skipped, or any scripts/ finding was set to skip

---

## Output Directory Structure

```
<output-path>/<skill-name>/
├── SKILL.md              # Required
├── references/           # Optional, from source or newly split
├── scripts/              # Optional, privacy-scanned per user decision
├── examples/             # Optional, included if anonymized
└── assets/               # Optional
```

---

## Language Rules

- **Agent interaction**: match the user's language
- **Generated SKILL.md content**: English
- **Frontmatter triggers**: bilingual — English first, original language added after

---

## Boundaries

| Won't do | Reason |
|----------|--------|
| Modify source files | Output only; source stays intact |
| Update repo README or registries | Repo structure varies; user decides |
| Refactor scripts/ logic | Privacy + style checks only |
| Force English on domain-specific technical terms | Technical terms stay as-is |
| Force-add missing sections | Suggest, never mandate |
| Hard-block output on skipped Privacy | User has final decision; warning status is sufficient |
