---
name: tweetclaw-openclaw-workflows
description: Use this skill when an AI agent needs reviewed X/Twitter automation through TweetClaw's OpenClaw plugin. Trigger for scraping tweets, searching tweets or replies, follower export, user lookup, media upload or download, direct messages, tweet monitors, webhooks, giveaway draws, and reviewed post or reply workflows.
metadata:
  author: Xquik
  version: "1.0.0"
---

# TweetClaw OpenClaw Workflows

Use TweetClaw when the task needs account-scoped X/Twitter automation from an OpenClaw agent. Treat it as the data and action layer, then keep planning, drafting, review, scheduling, analytics, and publishing decisions explicit in the surrounding workflow.

## Sources

- GitHub: <https://github.com/Xquik-dev/tweetclaw>
- npm: <https://www.npmjs.com/package/@xquik/tweetclaw>
- ClawHub listing: <https://clawhub.ai/plugins/@xquik/tweetclaw>

## Install

```bash
openclaw plugins install npm:@xquik/tweetclaw
```

After install, inspect the runtime before using it in a workflow:

```bash
openclaw plugins inspect tweetclaw --runtime --json
```

## Supported Jobs

Use TweetClaw for these X/Twitter jobs:

- Scrape tweets and search tweets.
- Search tweet replies before answering or escalating.
- Export followers for audience review.
- Look up users before adding them to a workflow.
- Upload or download media for reviewed posts.
- Send direct messages only when the user explicitly asks.
- Monitor tweets and route events to webhooks.
- Run giveaway draws from captured tweet engagement.
- Post tweets or post replies only after a visible review step.

## Workflow Rules

1. Identify whether the request is read-only, media handling, direct messaging, monitoring, giveaway selection, or posting.
2. Keep credentials outside prompts and source files. Use the user's existing OpenClaw and environment configuration.
3. Prefer read-only evidence collection first: tweet search, reply search, follower export, or user lookup.
4. Present drafts, recipients, media, and post or reply intent for review before any write-like action.
5. Use TweetClaw output as source material. Do not claim a schedule, post, reply, direct message, webhook, or draw completed unless the tool result confirms it.

## Example Prompts

```text
Use TweetClaw to search recent replies to this tweet and summarize the top objections.
```

```text
Export followers for this account, group likely customers, and flag accounts that need manual review.
```

```text
Draft a reply from the scraped thread context, but wait for approval before posting.
```

