# Agent Skills

A collection of useful skills for AI coding agents. Skills are a lightweight, open format for extending AI agent capabilities with specialized knowledge and workflows.

[Agent Skills](https://agentskills.io/) is the referenced standard.

## Installation

```bash
npx skills add Ciro-Gallo/awesome-agent-skills
```

## Usage

Skills are automatically available once installed. The agent will use them when relevant tasks are detected.

## Included Skills

- [coding-best-practices](coding-best-practices/SKILL.md) - Production-grade coding workflow guidance.
- [tweetclaw-openclaw-workflows](tweetclaw-openclaw-workflows/SKILL.md) - X/Twitter automation workflows for TweetClaw's OpenClaw plugin.

**Examples:**
```
Do commit files
```
```
Add a new interface that decouples this Spring Boot controller from the service implementation
```
```
Use TweetClaw to search tweet replies before drafting a response
```
```
Export followers for @example and summarize audience segments
```


## Skill Structure

Each skill contains:
- `SKILL.md` - Instructions for the agent
- `scripts/` - Helper scripts for automation (optional)
- `references/` - Supporting documentation (optional)

## License

MIT
