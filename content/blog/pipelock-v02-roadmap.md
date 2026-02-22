---
title: "What's next for Pipelock: the v0.2 roadmap"
date: 2026-02-11
author: "luckyPipewrench"
summary: "GitHub Actions, MCP input scanning, smart DLP, and what Pipelock Pro will look like."
description: "The v0.2 roadmap for Pipelock. GitHub Actions integration, MCP input scanning, smart DLP, and the path to Pipelock Pro."
---

v0.1.5 just shipped. 750+ tests, 7-layer scanner pipeline, MCP proxy, integrity monitoring, project auditing, all in one binary with six dependencies. (Update: v0.2.6 is now out with 2,500+ tests, a 9-layer pipeline, GitHub Action, and Streamable HTTP MCP transport.) I also got listed on the [OWASP Solutions Landscape](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) for agentic AI security, which feels pretty good for a project built by a plumber.

Here's what's coming next.

## GitHub Action

This is the biggest thing shipping this week. A composite GitHub Action that runs Pipelock in your CI pipeline with three modes:

- **audit**: scan your repo for secrets, detect agent types, get a security score
- **git-scan-diff**: catch leaked API keys in pull request diffs before they hit main
- **integrity-check**: verify workspace files match a known-good manifest

Each mode writes a job summary with the results. You get findings as a JSON output you can pipe into other steps.

```yaml
- uses: luckyPipewrench/pipelock-action@v1
  with:
    mode: audit
    directory: '.'
```

That's it. No config required for the basics. You can pass a `pipelock.yaml` if you want custom DLP patterns or domain lists.

## MCP input scanning

Right now Pipelock scans MCP *responses* only. The MCP server is the untrusted party, so scanning what it sends back made sense as the first priority.

v0.2 adds scanning on the inbound side too. When you're wrapping a trusted tool like a database connector, you want to catch prompt injection attempts in the *request* before they reach the server. Defense in depth. The scan won't be identical to the response side since the threat model is different, but DLP and injection patterns will run both directions.

## Smart DLP

The current DLP scanner runs regex patterns against URLs and content. It works, but regex means false positives. A string that looks like an API key but is actually a test fixture ID triggers the same alert as a real credential.

Smart DLP adds context awareness. If a value appears in a known config file, matches a test fixture naming pattern, or sits in a clearly non-sensitive context, the scanner can lower its confidence instead of blocking outright. This is the feature that separates "useful in CI" from "useful in production."

This is also where the Pro tier starts. The open source version keeps the regex-based scanner forever. Smart DLP with lower false positive rates becomes a Pro feature.

## Pipelock Pro

Pipelock is free, open source, and staying that way. Every feature in v0.1.5 ships in the open source binary. The Pro tier adds things teams need at scale:

- Web dashboard with live scan results and metrics
- Smart DLP with context-aware false positive reduction
- Fleet config management across multiple agents
- Slack and email alerts on rule trips
- Advanced audit log search and export
- Priority support

If you want early access, [drop your email on the waitlist](https://pipelab.org/pipelock/).

## Get involved

The code is at [github.com/luckyPipewrench/pipelock](https://github.com/luckyPipewrench/pipelock). Apache 2.0. I just expanded the [CONTRIBUTING guide](https://github.com/luckyPipewrench/pipelock/blob/main/CONTRIBUTING.md) with architecture docs, testing patterns, and recipes for adding new scanner layers.

If you're running AI agents in production and care about security, give it a shot. And if you break something, open an issue. That's how this gets better.
