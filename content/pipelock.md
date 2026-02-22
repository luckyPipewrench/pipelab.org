---
title: "Pipelock"
subtitle: "Open-source agent firewall."
---

AI coding agents like Claude Code and Cursor have shell access, API keys, and unrestricted network. If an agent gets compromised through prompt injection or a malicious MCP server, it can exfiltrate everything.

Pipelock is an [agent firewall](/agent-firewall/) — it enforces **capability separation** between the agent and the network. The agent process keeps the secrets but can't reach the internet directly. All traffic goes through Pipelock's scanning proxy.

```
Agent (secrets, no network) --> Pipelock (no secrets, has network) --> Internet / MCP Servers
```

## Two Proxy Modes

**Fetch proxy** — the agent's HTTP client points at Pipelock. Every request and response goes through a 9-layer scanner pipeline. Works with any agent framework that supports `HTTPS_PROXY`.

**MCP proxy** — wraps any MCP server (stdio or Streamable HTTP). Scans tool arguments for credential leaks, tool responses for prompt injection, and tool descriptions for poisoned instructions. Detects mid-session description changes (rug-pulls).

## Scanner Pipeline

1. **Scheme enforcement** — HTTP/HTTPS only
2. **Domain blocklist** — Configurable deny/allow lists per mode
3. **DLP** — 15+ regex patterns for API keys, tokens, credentials. Handles base64, hex, URL-encoding.
4. **Env variable leak detection** — Raw + base64-encoded, Shannon entropy filtering
5. **Path entropy** — Flag high-entropy URL segments (exfiltrated data)
6. **Subdomain entropy** — Flag DNS exfiltration attempts
7. **SSRF protection** — Block private IPs, link-local, metadata endpoints. DNS rebinding protection.
8. **Rate limiting** — Per-domain sliding window
9. **Data budgets** — Per-domain byte limits prevent slow-drip exfiltration

Response scanning adds prompt injection detection on all fetched content and MCP responses.

## GitHub Action

Run Pipelock as a CI check on every pull request. Scans diffs for exposed credentials, injection patterns, and security misconfigurations.

```yaml
- name: Pipelock Scan
  uses: luckyPipewrench/pipelock@v0.2.6
  with:
    scan-diff: 'true'
    fail-on-findings: 'true'
```

[View on GitHub Marketplace →](https://github.com/marketplace/actions/pipelock-agent-security-scan)

## Install

```bash
# Homebrew (macOS / Linux)
brew install luckyPipewrench/tap/pipelock

# Go
go install github.com/luckyPipewrench/pipelock/cmd/pipelock@latest

# Docker
docker pull ghcr.io/luckypipewrench/pipelock:0.2.6
```

## Quick Start

```bash
# Scan your project and generate a config
pipelock audit .

# Start the proxy
pipelock run --config pipelock.yaml

# Point your agent at the proxy
HTTPS_PROXY=http://127.0.0.1:8888 claude
```

## OWASP Coverage

Pipelock maps to the [OWASP Agentic AI Top 10 (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/). See the full [OWASP mapping](https://github.com/luckyPipewrench/pipelock/blob/main/docs/owasp-mapping.md) and [independent OWASP AI Security Solutions Landscape listing](https://genai.owasp.org/ai-security-solutions-landscape/).

## Key Numbers

- Single binary, ~12MB, zero runtime dependencies
- 7 direct Go dependencies
- 2,500+ tests with race detector
- 96%+ coverage
- 6 preset configs (audit, balanced, strict, claude-code, cursor, generic-agent)
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
  <a href="/agent-firewall/" class="btn btn-ghost">What is an Agent Firewall?</a>
</div>
