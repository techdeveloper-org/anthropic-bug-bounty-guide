# Reconnaissance — Target Information Gathering

Recon = Testing se PEHLE target ke baare mein jaankari ikkhata karna.

Jitna zyada recon, utna focused testing. Random testing = time waste.

---

## Step 1: Public Information Gathering

### 1.1 Read the HackerOne Program Page Carefully

```
https://hackerone.com/anthropic?type=team
```

Note down:
- [ ] Exact in-scope assets (URLs, apps, APIs)
- [ ] Exact out-of-scope items
- [ ] Reward ranges per severity
- [ ] Any special rules (e.g., "don't test production with load")
- [ ] Previously disclosed reports (learn what already found)

### 1.2 Read Anthropic's API Documentation

```
https://docs.anthropic.com/en/api/
```

Map all endpoints:
```
POST /v1/messages
GET  /v1/models
POST /v1/messages/{id}/count_tokens
POST /v1/complete (legacy)
... etc
```

For each endpoint, note:
- What parameters it accepts
- What authentication it requires
- What it returns
- Any rate limits mentioned

### 1.3 Check Public GitHub Repositories

```bash
# Search for Anthropic-related code
site:github.com anthropic api
site:github.com claude api key
```

Look for:
- SDK source code (understand how auth works)
- Example apps (see how API is used)
- Any accidentally committed API keys (report these!)
- Open issues mentioning security concerns

---

## Step 2: Technical Reconnaissance

### 2.1 Enumerate Subdomains

```bash
# Using subfinder (install: go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest)
subfinder -d anthropic.com -o subdomains.txt

# Or online tools:
# https://crt.sh/?q=%.anthropic.com
# https://dnsdumpster.com/
```

Common Anthropic subdomains to check:
```
claude.ai
api.anthropic.com
console.anthropic.com
docs.anthropic.com
www.anthropic.com
```

### 2.2 HTTP Headers Analysis

```bash
curl -I https://claude.ai
curl -I https://api.anthropic.com/v1/messages

# Look for:
# - Server: (what web server)
# - X-Powered-By: (tech stack hints)
# - Security headers (missing ones = low severity finding)
# - Rate limit headers (X-RateLimit-*)
```

### 2.3 JavaScript File Analysis

Open claude.ai in browser → DevTools (F12) → Sources tab

Look for:
- API endpoints hardcoded in JS
- Internal URLs
- Feature flags
- Any secrets (unlikely but check)

```bash
# Download and analyze JS files
# Install: npm install -g js-beautify
curl https://claude.ai/assets/main.js | js-beautify > main_readable.js
grep -E "(api|endpoint|secret|key|token)" main_readable.js
```

### 2.4 Check Robots.txt and Sitemap

```bash
curl https://claude.ai/robots.txt
curl https://claude.ai/sitemap.xml
curl https://api.anthropic.com/robots.txt
```

Hidden paths sometimes mentioned here.

---

## Step 3: API Reconnaissance

### 3.1 Set Up API Testing Environment

```bash
# Install httpie (easier than curl)
pip install httpie

# Set your API key
export ANTHROPIC_API_KEY="sk-ant-your-key-here"

# Test basic connectivity
http POST https://api.anthropic.com/v1/messages \
  x-api-key:$ANTHROPIC_API_KEY \
  anthropic-version:2023-06-01 \
  model=claude-3-haiku-20240307 \
  max_tokens:=10 \
  messages:='[{"role":"user","content":"hi"}]'
```

### 3.2 Map Authentication Methods

```bash
# What happens with no auth?
curl https://api.anthropic.com/v1/messages -X POST

# What happens with invalid auth?
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: invalid-key" -X POST

# What error format is returned?
# Note: error structure, error codes, any info disclosure
```

### 3.3 Test Endpoint Discovery

```bash
# Try common paths that aren't documented
curl https://api.anthropic.com/v1/users
curl https://api.anthropic.com/v1/admin
curl https://api.anthropic.com/v1/internal
curl https://api.anthropic.com/health
curl https://api.anthropic.com/metrics

# Note: 404 vs 401 vs 403 tells you if endpoint exists
# 401/403 = endpoint exists but you're not authorized (interesting!)
# 404 = endpoint doesn't exist
```

---

## Step 4: Create Your Target Map

After recon, create a simple map:

```markdown
## Anthropic Attack Surface Map

### Web Application (claude.ai)
- Login/Signup: /login, /signup
- Chat Interface: /conversation
- Settings: /settings
- Account: /account
- Team/Organization: /team

### API (api.anthropic.com)
- Messages: POST /v1/messages
- Models: GET /v1/models
- Files: POST/GET /v1/files (if applicable)
- Count tokens: POST /v1/messages/{id}/count_tokens

### Authentication
- Method: API keys (x-api-key header)
- Session: JWT/cookies on claude.ai
- OAuth: If applicable

### Interesting Areas (to test first)
1. Conversation history access
2. File upload/download
3. API key management
4. Team member management
5. Usage/billing endpoints
```

---

## Recon Checklist

Before starting active testing, verify:

- [ ] Read full HackerOne program page
- [ ] Listed all in-scope assets
- [ ] Noted all out-of-scope items
- [ ] Read API documentation completely
- [ ] Set up 2 test accounts
- [ ] Configured Burp Suite proxy
- [ ] Mapped all API endpoints
- [ ] Checked subdomains
- [ ] Analyzed HTTP headers
- [ ] Created target map

---

## Next Step

→ [API Testing Methodology](api-testing.md)
→ [Authentication Testing](auth-testing.md)
