# Anthropic Bug Bounty Guide 🔐

> A community guide to responsibly finding and reporting security vulnerabilities in Anthropic's products via HackerOne.

[![HackerOne](https://img.shields.io/badge/HackerOne-Anthropic-red)](https://hackerone.com/anthropic?type=team)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg)](CONTRIBUTING.md)

---

## What Is This?

[Anthropic](https://anthropic.com) — the company behind Claude AI — runs an **official Bug Bounty program** on HackerOne. This means:

- ✅ You are **legally authorized** to test Anthropic's systems (within scope)
- ✅ You can **earn bounties** (rewards) for valid security findings
- ✅ You help make AI safer for everyone

This guide explains:
1. What the bug bounty program is
2. How HackerOne's platform standards work
3. How to find, document, and submit vulnerabilities
4. How to use AI security agents to assist your research

---

## Quick Start

1. **Create a HackerOne account** → [hackerone.com](https://hackerone.com)
2. **Join Anthropic's program** → [hackerone.com/anthropic](https://hackerone.com/anthropic?type=team)
3. **Read the program policy** (scope, out-of-scope, rewards)
4. **Read this guide** → follow [docs/](docs/) folder
5. **Start testing!** → follow [methodology/](methodology/)

---

## Table of Contents

| Document | What You'll Learn |
|----------|-------------------|
| [docs/what-is-bug-bounty.md](docs/what-is-bug-bounty.md) | Bug bounty basics, legal authorization, rewards |
| [docs/hackerone-standards.md](docs/hackerone-standards.md) | HackerOne platform standards (IDOR, severity, etc.) |
| [docs/anthropic-program.md](docs/anthropic-program.md) | Anthropic's specific scope, targets, out-of-scope |
| [docs/severity-rating.md](docs/severity-rating.md) | How to rate severity (CVSS v4.0, HackerOne standards) |
| [methodology/recon.md](methodology/recon.md) | Reconnaissance — gathering target info |
| [methodology/api-testing.md](methodology/api-testing.md) | API security testing methodology |
| [methodology/auth-testing.md](methodology/auth-testing.md) | Authentication & authorization testing |
| [methodology/report-writing.md](methodology/report-writing.md) | How to write a good HackerOne report |
| [tools/ai-agents.md](tools/ai-agents.md) | Using AI security agents to assist research |
| [examples/](examples/) | Real report examples (redacted) |

---

## Who Is This For?

- 🆕 **Beginners** — Never done bug bounty before? Start from [docs/what-is-bug-bounty.md](docs/what-is-bug-bounty.md)
- 🔁 **Intermediate** — Know basics but new to AI targets? See [docs/anthropic-program.md](docs/anthropic-program.md)
- 🏆 **Advanced** — Want to use AI agents for automated testing? See [tools/ai-agents.md](tools/ai-agents.md)

---

## Why Anthropic's Bug Bounty Matters

Anthropic is building one of the most powerful AI systems in the world. Security vulnerabilities in Claude's API or platform could:

- Expose **user data** and conversations
- Enable **prompt injection** attacks
- Allow **unauthorized access** to AI capabilities
- Compromise the **safety mechanisms** that keep Claude aligned

By reporting vulnerabilities responsibly, you're helping make AI safer for everyone. 🌍

---

## Community & Contributing

Found something wrong in this guide? Have a tip to share?

- **Open an Issue** → [GitHub Issues](../../issues)
- **Submit a PR** → See [CONTRIBUTING.md](CONTRIBUTING.md)
- **Discuss** → [GitHub Discussions](../../discussions)

---

## Important Rules

⚠️ **ALWAYS:**
- Test only within the **approved scope** listed on HackerOne
- Follow **responsible disclosure** — report to Anthropic FIRST
- Follow HackerOne's **code of conduct**
- Stop testing if you find something sensitive (PII, private data)

🚫 **NEVER:**
- Access other users' data beyond what's needed to prove the bug
- Run automated scanners that cause DoS/service disruption
- Share vulnerabilities publicly before they're fixed (coordinated disclosure)
- Test production systems with destructive payloads

---

## Disclaimer

This guide is for **educational purposes** and **authorized security research only**. Always read Anthropic's program policy before testing. The authors are not responsible for misuse of this information.

---

*Made with ❤️ by the security community | Contributions welcome!*
