---
type: readme
last_updated: 2026-05
---

# Wendy Tan — Personal Knowledge Base

## What This Is
A portable, AI-ready personal knowledge base. Every file is plain Markdown.
The KB is designed to be injected into any AI platform (Claude, ChatGPT, Perplexity,
Cursor, API calls, etc.) to give it accurate, up-to-date context about who I am,
what I know, what I'm working on, and how I like to work.

## How to Use

### Quick inject (any AI platform)
Paste or upload `context.md` at the start of any new session. This gives the AI
enough context to respond accurately without loading the full KB.

### Full context (Claude Projects, Perplexity Spaces, etc.)
Upload the entire `kb/` folder as a knowledge base. The AI can then query individual
files for deeper context on specific skills, projects, or preferences.

### API / local LLM
Feed `context.md` as the system prompt. For larger context windows, concatenate
`context.md` + the relevant `skills/` or `projects/` file for the task at hand.

### Cursor / Copilot
Copy `preferences/ai-interaction.md` content into `.cursorrules` or your Copilot
instructions file.

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

## Maintenance

### When to update
- Skill level changes → update the relevant `skills/` file
- Project status changes → update the relevant `projects/` file
- New project starts → copy `projects/_template.md`, fill in, commit
- Learning goals shift → update `goals/current.md`
- Preferences change → update `preferences/ai-interaction.md`
- After any update → regenerate `context.md` to reflect the change

### Versioning
- This repo is Git-tracked — commit every update with a meaningful message
- Use `last_updated` front matter in every file to track freshness
- Tag significant milestones (e.g. `v1.0-2026-05` when KB is first complete)

## Quick Start for a New AI Session

1. Open a new chat on any AI platform
2. Paste the contents of `context.md` as your first message or system prompt
3. Start your task — the AI now has full context about who you are and how you work
4. For deep project work, also paste the relevant `projects/` file