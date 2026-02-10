---
title: "283 ClawHub Skills Are Leaking Your Secrets"
date: 2026-02-09
summary: "Snyk found 283 out of 3,984 ClawHub skills reference hardcoded credentials. VirusTotal's Code Insight catches some of these. Pipelock catches the rest at runtime."
---

Snyk found 283 out of 3,984 ClawHub skills reference hardcoded credentials. VirusTotal's Code Insight catches some of these statically. Pipelock catches the rest at runtime, when the exfiltration actually happens.

This post breaks down what the scanners miss and why runtime DLP scanning is the missing layer.

[Read the full post on GitHub Pages](https://luckypipewrench.github.io/pipelock/blog/2026/02/09/leaky-clawhub-skills-runtime-protection/)
