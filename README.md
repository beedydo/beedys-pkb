---
type: readme
last_updated: 2026-06
---

# Wendy Tan — Personal Knowledge Base

## What This Is
My personal knowledge base — everything about me, my work, and how I think, in plain Markdown.
I use this to give any AI tool accurate, up-to-date context without re-explaining myself every session.
Covers who I am, what I know, what I'm working on, and how I like to work.

## How I Use It

### Quick inject (any AI platform)
Paste or upload `context.md` at the start of a new session. Fast way to get an AI up to speed without dumping the whole KB.

### Full context (Claude Projects, Perplexity Spaces, etc.)
Upload the whole `kb/` folder as a knowledge base. Good for ongoing work where I want the AI to pull from specific files on demand.

### API / local LLM
Use `context.md` as the system prompt. For bigger context windows, I concatenate it with the relevant `skills/` or `projects/` file for whatever I'm working on.

### Cursor / Copilot
Copy `preferences/ai-interaction.md` into `.cursorrules` or my Copilot instructions.

## Folder Structure

```
kb/
├── README.md                          ← This file
├── context.md                         ← Quick-inject summary for any AI session
│
├── profile/
│   └── about.md                       ← Full professional profile, role, team context
│
├── skills/
│   ├── terraform.md
│   ├── aws.md
│   ├── ansible-aap.md
│   ├── gitlab-cicd.md
│   ├── databricks.md
│   ├── zscaler-aem.md
│   ├── python.md
│   ├── bash-shell.md
│   ├── yaml.md
│   ├── networking.md
│   └── security-iam.md
│
├── projects/
│   ├── amp-automation-management-platform.md
│   ├── aip-asset-intelligence-platform.md
│   └── iac-self-service-extraction-tool.md
│
├── preferences/
│   └── ai-interaction.md              ← How I like AI to respond — paste into any platform
│
└── goals/
    └── current.md                     ← Active learning and career goals
```

## Keeping It Up to Date

- Skill level changes → update the relevant `skills/` file
- Project status changes → update the relevant `projects/` file
- New project → copy `projects/_template.md`, fill in, commit
- Learning goals shift → update `goals/current.md`
- Preferences change → update `preferences/ai-interaction.md`
- After any update → regenerate `context.md`

Git-tracked. Commit every update with a meaningful message. `last_updated` front matter in every file so I know what's stale.

## Starting a New AI Session

1. Open a new chat
2. Paste `context.md` as my first message or system prompt
3. For deep project work, also paste the relevant `projects/` file