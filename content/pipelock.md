---
title: "Pipelock"
subtitle: "All-in-one security harness for AI agents."
---

AI coding agents like Claude Code and Cursor have shell access, API keys, and unrestricted network. If an agent gets compromised through prompt injection or a malicious MCP server, it can exfiltrate everything.

Pipelock fixes this with **capability separation**. The agent process keeps the secrets but can't reach the internet. All HTTP traffic goes through Pipelock's scanning proxy, which checks every request and response.

```
Agent (secrets, no network) --> Pipelock Proxy (no secrets, full network) --> Internet
```

## 7-Layer Scanner Pipeline

1. **SSRF Protection** -- Block private IPs, metadata endpoints, DNS rebinding
2. **Domain Blocklist** -- Configurable deny/allow lists
3. **Rate Limiting** -- Per-domain sliding window
4. **DLP** -- Regex patterns for API keys, tokens, credentials
5. **Env Leak Detection** -- Raw + base64-encoded environment variables
6. **Entropy Analysis** -- Flag high-entropy URL segments
7. **URL Length Limits** -- Configurable max URL length

Response scanning adds prompt injection detection on fetched content.

## MCP Proxy

Wraps any MCP server as a stdio proxy. Scans JSON-RPC 2.0 responses for prompt injection and credential leaks before they reach the agent.

```
pipelock mcp proxy -- npx @modelcontextprotocol/server-filesystem /tmp
```

## Install

```
# Homebrew (macOS / Linux)
brew install luckyPipewrench/tap/pipelock

# Go
go install github.com/luckyPipewrench/pipelock/cmd/pipelock@latest

# Docker
docker pull ghcr.io/luckypipewrench/pipelock:0.1.4
```

## Quick Start

```
# Generate a config
pipelock generate config --preset balanced -o pipelock.yaml

# Start the proxy
pipelock run --config pipelock.yaml

# Point your agent at the proxy
HTTP_PROXY=http://localhost:8888 claude
```

## OWASP Coverage

Pipelock maps to the [OWASP Agentic AI Top 10 (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/), covering agent goal hijack, tool misuse, supply chain vulnerabilities, and more. See the full [OWASP mapping](https://github.com/luckyPipewrench/pipelock/blob/main/docs/owasp-mapping.md).

## Key Numbers

- Single binary, ~12MB, zero runtime dependencies
- 6 direct Go dependencies
- 660+ tests with race detector
- 90%+ coverage across all packages
- Apache 2.0 license

<div class="waitlist-section">
  <h2 class="waitlist-heading">Pipelock Pro</h2>
  <p class="waitlist-intro">Pipelock is free, open source, and always will be. But we're building a Pro tier for teams that need more.</p>
  <ul class="waitlist-features">
    <li>Web dashboard with live scan results and metrics</li>
    <li>Smart DLP with lower false positive rates</li>
    <li>Fleet config management across multiple agents</li>
    <li>Slack and email alerts on rule trips</li>
    <li>Advanced audit log search and export</li>
    <li>Priority support</li>
  </ul>
  <p class="waitlist-cta-text">Drop your email. We'll let you know when it's ready.</p>
  <form class="waitlist-form" action="https://buttondown.com/api/emails/embed-subscribe/luckypipewrench" method="post" target="_blank">
    <input type="hidden" name="tag" value="pipelock-pro" />
    <input type="hidden" name="embed" value="1" />
    <div class="waitlist-input-row">
      <input type="email" name="email" placeholder="you@example.com" required class="waitlist-email" />
      <button type="submit" class="btn btn-primary waitlist-submit">Notify Me</button>
    </div>
  </form>
  <p class="waitlist-note">No spam. One email when Pro launches.</p>
</div>

<div class="hero-buttons" style="margin-top: 2rem;">
  <a href="https://github.com/luckyPipewrench/pipelock" class="btn btn-primary">GitHub</a>
  <a href="https://asciinema.org/a/I1UzzECkeCBx6p42" class="btn btn-ghost">Watch Demo</a>
</div>
