# Using AI Security Agents for Bug Bounty Research

This guide explains how to use the [claude-global-library](https://github.com/techdeveloper-org/claude-global-library) security agent ecosystem to accelerate your bug bounty research.

---

## What Are Security Agents?

The claude-global-library contains **24 specialized security agents** built on top of Claude AI. These agents are domain experts that can:

- Analyze API specifications for security flaws
- Generate threat models using STRIDE/PASTA methodology
- Calculate CVSS scores with mathematical precision
- Identify vulnerability patterns in code
- Draft HackerOne-quality reports

---

## Available Security Agents

### Core Security Agents (Domain 19 — Cybersecurity)

| Agent | Role | Best Used For |
|-------|------|---------------|
| `threat-modeling-specialist` | STRIDE/PASTA attack trees | Phase 1: Initial threat modeling |
| `api-security-auditor` | OWASP API Top 10 | Finding API vulnerabilities |
| `auth-security-specialist` | OAuth 2.1, JWT, session attacks | Authentication testing |
| `penetration-tester` | Chained exploits, PoC development | Phase 3: Active exploitation |
| `sast-engineer` | Static code analysis patterns | Code review |
| `secrets-detection-specialist` | Entropy analysis, credential hunting | Finding leaked secrets |
| `dependency-vulnerability-analyst` | CVE + CVSS×EPSS×KEV scoring | Third-party component analysis |
| `infrastructure-security-auditor` | CIS benchmarks, K8s, Docker | Infrastructure review |
| `crypto-security-specialist` | TLS, certificates, timing attacks | Cryptography review |
| `security-compliance-mapper` | ISO 27001, NIST, regulatory mapping | Compliance gap analysis |
| `security-lead-auditor` | BINARY verdict, CVSS v4.0 | Final severity scoring |
| `cyber-mathematics-expert` | CVSS calculations, FAIR ALE, EPSS | Mathematical severity proof |

### Supporting Agents

| Agent | Role | When To Use |
|-------|------|-------------|
| `llm-security-mathematics-expert` | LLM-specific attack math | Claude API specific testing |
| `threat-hunter` | Active threat indicator hunting | Proactive threat detection |
| `threat-intelligence-analyst` | Historical threat context | Prior vulnerability research |
| `cloud-security-architect` | AWS/GCP/Azure misconfigurations | Cloud infrastructure testing |
| `iam-security-engineer` | IAM/RBAC policy analysis | Permission testing |
| `ai-ml-security-engineer` | AI/ML model attack vectors | LLM-specific vulnerabilities |

---

## Phase F — Security Audit Pipeline

This is the recommended orchestration order for Anthropic bug bounty:

```
Phase F.0  →  Requirements & Target Gathering
Phase F.1  →  Threat Modeling (threat-modeling-specialist)
Phase F.2  →  Code & Dependency Analysis (sast + secrets + deps)
Phase F.3  →  API Security (api-security-auditor + auth-security-specialist)
Phase F.4  →  Infrastructure & Crypto (infra + crypto + cloud)
Phase F.5  →  Penetration Testing (penetration-tester)
Phase F.6  →  Synthesis & Scoring (security-lead-auditor + cyber-mathematics-expert)
```

---

## How to Use Each Agent

### Step 1: Threat Modeling

Invoke the `threat-modeling-specialist` agent with your target information:

```markdown
Target: Anthropic Claude API (api.anthropic.com)

Architecture:
- REST API with JSON payloads
- Authentication: API keys (Bearer tokens)
- Endpoints: /v1/messages, /v1/models, /v1/files, etc.
- Users: Developers integrating Claude into their apps

Please perform STRIDE threat modeling and generate threat_list.json.
Focus on: Spoofing, Tampering, Information Disclosure, Elevation of Privilege.
```

**Output:** `threat_list.json` with ranked threats by severity.

---

### Step 2: API Security Audit

Invoke `api-security-auditor` with the API specification:

```markdown
Target API: https://api.anthropic.com

Available endpoints (from documentation):
- POST /v1/messages
- GET /v1/models
- POST /v1/messages/{id}/count_tokens
- Files API endpoints

Authentication: x-api-key header

Please audit for OWASP API Security Top 10:
- API1: BOLA (Broken Object Level Authorization)
- API2: Broken Authentication
- API3: Broken Object Property Level Authorization
- API4: Unrestricted Resource Consumption
- API5: BFLA (Broken Function Level Authorization)
- API6: Server-Side Request Forgery (SSRF)
- API7: Security Misconfiguration
- API8: Lack of Protection from Automated Threats
- API9: Improper Inventory Management
- API10: Unsafe Consumption of APIs

Output: api_findings.json with CVSS scores per finding.
```

---

### Step 3: CVSS Scoring

Invoke `cyber-mathematics-expert` for precise CVSS v4.0 calculations:

```markdown
Calculate CVSS v4.0 score for this finding:

Finding: Authenticated user can access another user's conversation history
by modifying the conversation_id in the API request. No authorization check
is performed beyond API key authentication.

Metrics:
- Attack Vector: Network (API is internet-facing)
- Attack Complexity: Low (conversation IDs may be guessable or leaked)
- Privileges Required: Low (needs valid API key)
- User Interaction: None
- Confidentiality Impact: High (full conversation history exposed)
- Integrity Impact: None (read-only)
- Availability Impact: None

Please calculate:
1. CVSS v4.0 Base Score
2. CVSS Vector String
3. MacroVector classification
4. HackerOne severity category
5. Estimated bounty range
```

---

### Step 4: Report Drafting

After findings, use the report template from [methodology/report-writing.md](../methodology/report-writing.md) and populate it with the agent outputs.

---

## Example Workflow

```bash
# 1. Set up claude-global-library (from README)
cd ~/claude-global-library

# 2. Start with threat modeling
# In Claude Code CLI:
# /threat-modeling-specialist
# → provide target info
# → get threat_list.json

# 3. Move to API security
# /api-security-auditor
# → provide API spec + threat_list.json
# → get api_findings.json

# 4. Score with math expert
# /cyber-mathematics-expert
# → provide finding details
# → get CVSS v4.0 score

# 5. Draft report
# Use report template + agent outputs
# Submit to HackerOne
```

---

## Important Notes on Agent Usage

### Scope
- Agents help with **analysis and methodology** — they don't automatically hack targets
- You still need to manually test, reproduce, and verify findings
- Agents assist with: threat modeling, vulnerability pattern identification, CVSS scoring, report writing

### Legal
- Always test only within the authorized scope of the HackerOne program
- Agents don't bypass this requirement — they're research tools
- You're responsible for all testing activities

### Quality
- Agent outputs are starting points, not final reports
- Verify every finding manually before submitting
- HackerOne requires proof-of-concept — agents can help draft it, you must verify it

---

## Contributing Security Agents

If you want to improve the security agent ecosystem:

1. Fork [claude-global-library](https://github.com/techdeveloper-org/claude-global-library)
2. Add new agents in `agents/{name}/agent.md`
3. Add corresponding skills in `skills/{name}/SKILL.md`
4. Update INDEX.md and other catalog files
5. Submit PR

See the [claude-global-library CLAUDE.md](https://github.com/techdeveloper-org/claude-global-library/blob/main/CLAUDE.md) for agent creation guidelines.
