# Deploying walat.eu to GitHub Pages

**Date:** October 18, 2025
**Target:** Deploy Hugo site to GitHub Pages with custom domain walat.eu

## Prerequisites

- GitHub account (you have this)
- Access to DNS settings for walat.eu domain
- Hugo site ready in `/home/d/project/walat.eu/main`

---

## Part 1: GitHub Repository Setup

### 1.1 Create GitHub Repository

1. Go to https://github.com/new
2. Repository name: `walat.eu` (or your preferred name)
3. Set to **Public** (required for free GitHub Pages)
4. **Do NOT** initialize with README (we'll push existing code)
5. Click "Create repository"

### 1.2 Initialize Git and Push Code

```bash
cd /home/d/project/walat.eu/main

# Initialize git if not already done
git init

# Add all files
git add .

# Create initial commit
git commit -m "Initial commit: Hugo site for walat.eu"

# Add GitHub remote (replace USERNAME with your GitHub username)
git remote add origin git@github.com:USERNAME/walat.eu.git

# Push to GitHub
git branch -M main
git push -u origin main
```

---

## Part 2: GitHub Actions Workflow Setup

### 2.1 Create Workflow Directory

```bash
mkdir -p .github/workflows
```

### 2.2 Create Hugo Deployment Workflow

Create file `.github/workflows/hugo.yaml` with this content:

```yaml
# Workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.151.2
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### 2.3 Commit and Push Workflow

```bash
git add .github/workflows/hugo.yaml
git commit -m "Add GitHub Actions workflow for Hugo deployment"
git push
```

---

## Part 3: GitHub Pages Configuration

### 3.1 Enable GitHub Pages

1. Go to your repository on GitHub
2. Click **Settings** → **Pages** (in left sidebar)
3. Under "Build and deployment":
   - Source: Select **GitHub Actions**
4. Save changes

### 3.2 Create CNAME File for Custom Domain

```bash
# Create CNAME file in static directory
echo "walat.eu" > static/CNAME

git add static/CNAME
git commit -m "Add CNAME file for custom domain"
git push
```

---

## Part 4: DNS Configuration

### 4.1 DNS Records to Add

Log in to your DNS provider (where you manage walat.eu) and add these records:

#### For Apex Domain (walat.eu)

**Add 4 A Records:**

| Type | Name | Value | TTL |
|------|------|-------|-----|
| A | @ | 185.199.108.153 | 3600 |
| A | @ | 185.199.109.153 | 3600 |
| A | @ | 185.199.110.153 | 3600 |
| A | @ | 185.199.111.153 | 3600 |

#### For www Subdomain (www.walat.eu)

**Add CNAME Record:**

| Type | Name | Value | TTL |
|------|------|-------|-----|
| CNAME | www | USERNAME.github.io | 3600 |

*Replace `USERNAME` with your actual GitHub username*

#### Optional: IPv6 Support (AAAA Records)

| Type | Name | Value | TTL |
|------|------|-------|-----|
| AAAA | @ | 2606:50c0:8000::153 | 3600 |
| AAAA | @ | 2606:50c0:8001::153 | 3600 |
| AAAA | @ | 2606:50c0:8002::153 | 3600 |
| AAAA | @ | 2606:50c0:8003::153 | 3600 |

### 4.2 Verify DNS Configuration

After adding DNS records (wait 5-10 minutes for propagation):

```bash
# Check A records
dig walat.eu +noall +answer

# Should show the 4 GitHub Pages IP addresses

# Check CNAME record
dig www.walat.eu +noall +answer

# Should show USERNAME.github.io
```

---

## Part 5: Configure Custom Domain in GitHub

### 5.1 Add Custom Domain

1. Go to repository **Settings** → **Pages**
2. Under "Custom domain", enter: `walat.eu`
3. Click **Save**
4. Wait for DNS check to complete (may take a few minutes)

### 5.2 Enable HTTPS

1. Once DNS check passes, check the box: **Enforce HTTPS**
2. GitHub will automatically provision a Let's Encrypt certificate
3. This may take up to 24 hours for first-time setup

---

## Part 6: Update Hugo Configuration

### 6.1 Update baseURL in hugo.toml

```toml
baseURL = 'https://walat.eu/'
```

### 6.2 Commit and Deploy

```bash
git add hugo.toml
git commit -m "Update baseURL to production domain"
git push
```

This will trigger the GitHub Actions workflow automatically.

---

## Verification Checklist

After deployment (wait 5-10 minutes for first deploy):

- [ ] Visit https://USERNAME.github.io/REPO-NAME/ - should redirect to walat.eu
- [ ] Visit http://walat.eu - should load your site
- [ ] Visit https://walat.eu - should load with HTTPS (may take up to 24h first time)
- [ ] Visit http://www.walat.eu - should redirect to walat.eu
- [ ] Visit https://www.walat.eu - should redirect to walat.eu
- [ ] Check GitHub Actions tab - workflow should show green checkmark
- [ ] Verify all pages load correctly
- [ ] Check that `/series/ai-agents-in-practice/` works

---

## Troubleshooting

### DNS Not Propagating

```bash
# Check current DNS status
dig walat.eu
dig www.walat.eu

# If showing old values, wait longer (up to 24 hours)
# Use this to check from different DNS servers:
nslookup walat.eu 8.8.8.8
```

### GitHub Actions Workflow Failing

1. Go to **Actions** tab in repository
2. Click on failed workflow
3. Check error messages in build log
4. Common issues:
   - Hugo version mismatch
   - Missing dependencies
   - Syntax errors in content

### HTTPS Certificate Not Provisioning

- Wait up to 24 hours for first-time setup
- Ensure DNS is correctly configured
- Check that "Enforce HTTPS" is enabled in Settings → Pages
- Try unchecking and re-checking "Enforce HTTPS"

### 404 Errors on Site

- Check that `baseURL` in `hugo.toml` matches your domain
- Verify CNAME file contains correct domain
- Ensure GitHub Actions workflow completed successfully

---

## Timeline for Going Live

**Today (October 18, 2025):**
1. Create GitHub repository - **5 minutes**
2. Push code to GitHub - **5 minutes**
3. Set up GitHub Actions - **10 minutes**
4. Configure DNS records - **15 minutes**
5. Add custom domain in GitHub - **5 minutes**

**DNS propagation:** 1-24 hours (usually 1-4 hours)

**HTTPS certificate:** Up to 24 hours (usually 1-2 hours)

**For tomorrow evening publication:**
- DNS should be fully propagated
- HTTPS should be working
- Site will be live and accessible

---

## Post-Deployment Workflow

Every time you make changes:

```bash
# Make your edits to content files

# Commit changes
git add .
git commit -m "Descriptive commit message"

# Push to GitHub
git push

# GitHub Actions automatically builds and deploys (2-5 minutes)
```

---

## Security Notes

1. **Never commit sensitive data** to public repository
2. Keep `.env` files in `.gitignore`
3. Review `public/` directory before first deploy
4. HTTPS is mandatory - users will be redirected automatically

---

## Additional Resources

- [Hugo GitHub Pages Hosting Docs](https://gohugo.io/host-and-deploy/host-on-github-pages/)
- [GitHub Pages Custom Domain Docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)
- [GitHub Actions for Hugo](https://github.com/peaceiris/actions-hugo)

---

**Last Updated:** October 18, 2025
**Hugo Version:** 0.151.2
**Status:** Ready for deployment
