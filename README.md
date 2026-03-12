# Lawbrokr Engineering Vault

An [Obsidian](https://obsidian.md) vault that serves as an LLM-readable knowledge base for the Lawbrokr product ecosystem. It provides AI coding assistants with full context on active projects, architecture decisions, and workflow notes.

## Vault Structure

```
vault/
├── CLAUDE.md              ← AI assistant instructions (start here)
├── projects/              ← Full documentation for each product
│   ├── storefront.md
│   ├── justice-sync.md
│   ├── justice-frontend.md
│   └── chrome-extension.md
├── reference/             ← How-to guides and command references
│   ├── rails-commands.md
│   ├── sidekiq.md
│   └── postgres.md
├── tickets/               ← Research notes and client discussions
│   ├── bot-detection.md
│   ├── component-mapping.md
│   └── mclellan-law.md
├── features/              ← Feature specs and design links
│   ├── ai-voice.md
│   ├── flow-templates.md
│   └── internal-dashboard.md
├── daily/                 ← Daily notes
└── credentials/           ← Local-only secrets (gitignored)
```

## Key Terminology

These terms have been renamed in the codebase but may appear under old names:

| UI Name          | Code Name | What it is                        |
| ---------------- | --------- | --------------------------------- |
| Funnel           | Campaign  | Multi-step lead capture flow      |
| Workflow         | Journey   | Automated lead follow-up sequence |
| Marketing Source | Source    | Where a lead came from            |
