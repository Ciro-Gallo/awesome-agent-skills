# Agent Skills

A collection of useful skills for AI coding agents. Skills are a lightweight, open format for extending AI agent capabilities with specialized knowledge and workflows.

[Agent Skills](https://agentskills.io/) is the referenced standard.

## Installation

```bash
npx skills add Ciro-Gallo/awesome-agent-skills
```

## Skills

| Skill | Use for |
| --- | --- |
| [`coding-best-practices`](./coding-best-practices/) | Production-grade code edits, refactors, tests, and commits |
| [`x-twitter-scraper`](./x-twitter-scraper/) | Xquik X data workflows, MCP setup, webhooks, exports, monitoring, and gated publishing |

Xquik is an independent third-party service. Not affiliated with X Corp. "Twitter" and "X" are trademarks of X Corp.

## Usage

Skills are automatically available once installed. The agent will use them when relevant tasks are detected.

**Examples:**
```
Do commit files
```
```
Add a new interface that decouples this Spring Boot controller from the service implementation
```


## Skill Structure

Each skill contains:
- `SKILL.md` - Instructions for the agent
- `scripts/` - Helper scripts for automation (optional)
- `references/` - Supporting documentation (optional)

## License

MIT
