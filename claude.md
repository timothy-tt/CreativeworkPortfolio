# Actions Log - January 18, 2026

## Summary
Set up self-hosted deployment pipeline for CreativeworkPortfolio at `creativeworks.portfolio.zegoe.com`.

---

## 1. Created GitHub Repository
```bash
gh repo create CreativeworkPortfolio --private
```
Created private repository: https://github.com/timothy-tt/CreativeworkPortfolio

---

## 2. Built Single-Page Portfolio Website
**File:** `index.html`

Created a dark-themed single-page HTML portfolio with:
- Timeline navigation on the right side (2014-2025)
- Year sections with project card grids
- Active year highlighting on scroll
- Progress bar at top
- Responsive design

Header displays:
- h1: Creative Works Portfolio
- h2: Timothy Ton-That
- h3: For IMT-Solutions
- h4: As of January 2026

---

## 3. Created GitHub Actions Workflow
**File:** `.github/workflows/deploy.yml`

```yaml
name: Deploy to Self-Hosted

on:
  push:
    branches:
      - '**'
  workflow_dispatch:

jobs:
  deploy:
    runs-on: self-hosted
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Deploy
        run: |
          echo "Deployed branch ${{ github.ref_name }} at $(date)"
          echo "Files served directly from repository checkout"
```

Triggers on any branch push.

---

## 4. Added Subdomain to Cloudflare DNS Script
**File:** `/home/tim/update-dns.sh`

Added `creativeworks.portfolio.zegoe.com` to the list of domains:
```bash
creativeworks.portfolio.zegoe.com \
```

---

## 5. Added Subdomain to Caddy Configuration
**File:** `/etc/caddy/Caddyfile`

```caddy
creativeworks.portfolio.zegoe.com {
    root * /home/tim/git/CreativeworkPortfolio
    file_server
    encode gzip

    # Security headers
    header {
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        Referrer-Policy "no-referrer-when-downgrade"
    }
}
```

Reloaded Caddy:
```bash
sudo systemctl reload caddy
```

---

## 6. Ran DNS Update
```bash
/home/tim/update-dns.sh
```
Updated Cloudflare DNS records for the new subdomain.

---

## 7. GitHub Actions Runner (Pending)

**Status:** Runner needs to be configured

The workflow is set up to use a self-hosted runner, but a dedicated runner was removed to avoid duplication.

**TODO:** Set up a shared runner at the organization level or register the existing PTCVietCAD runner for this repository.

Existing runner that could be shared:
- **Location:** `/home/tim/actions-runner-ptcvietcad`
- **Service:** `actions.runner.timothy-tt-PTCVietCADwebsite.ptcvietcad-runner.service`

---

## Final Configuration

| Component | Details |
|-----------|---------|
| **URL** | https://creativeworks.portfolio.zegoe.com |
| **Source** | `/home/tim/git/CreativeworkPortfolio` |
| **Web Server** | Caddy (auto HTTPS via Let's Encrypt) |
| **CI/CD** | GitHub Actions workflow ready, needs runner |

---

## Useful Commands

### Reload Caddy:
```bash
sudo systemctl reload caddy
```

### Manual DNS update:
```bash
/home/tim/update-dns.sh
```

### Check PTCVietCAD runner status:
```bash
systemctl status actions.runner.timothy-tt-PTCVietCADwebsite.ptcvietcad-runner.service
```
