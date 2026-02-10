# Deployment Guide

## Repository Structure

```
├── web/                 # Deployable website files
│   ├── index.html
│   ├── CNAME            # Custom domain for GitHub Pages
│   └── assets/          # Images, video, favicons, manifest
├── docs/                # Internal documents (not deployed)
├── .github/workflows/   # CI/CD
│   └── pages.yml        # GitHub Pages deploy workflow
├── README.md
└── DEPLOY.md            # This file
```

The `web/` folder is the deploy root. Only its contents are published.

---

## GitHub Pages

Deployment is automated via GitHub Actions.

### How it works

The workflow at `.github/workflows/pages.yml` runs on every push to `main`:

1. Checks out the repo
2. Uploads the `web/` folder as a Pages artifact
3. Deploys it to GitHub Pages

### Setup (already configured)

1. Go to **Settings > Pages** in the GitHub repo.
2. Under **Build and deployment > Source**, select **GitHub Actions**.
3. Push to `main` — the workflow runs automatically.

### Custom domain

The file `web/CNAME` contains `gridops.app`. GitHub Pages uses this to serve the site on the custom domain.

To change the domain, edit `web/CNAME` and update DNS:

- **Apex domain** (`gridops.app`): add `A` records pointing to GitHub's IPs:
  ```
  185.199.108.153
  185.199.109.153
  185.199.110.153
  185.199.111.153
  ```
- **www subdomain**: add a `CNAME` record pointing `www` to `<username>.github.io`.

### Manual trigger

You can also trigger a deploy without pushing code:

```
gh workflow run pages.yml
```

---

## Cloudflare Pages

Cloudflare Pages can be deployed via the Wrangler CLI.

### Prerequisites

Install Wrangler:

```
npm install -g wrangler
```

Log in to Cloudflare:

```
wrangler login
```

### First-time setup

Create the project (only needed once):

```
wrangler pages project create gridops --production-branch main
```

### Deploy

From the repo root, deploy the `web/` folder:

```
wrangler pages deploy web --project-name gridops --commit-dirty=true
```

The site will be available at:

- **Production:** https://gridops.pages.dev/
- **Per-deploy preview:** a unique URL printed after each deploy

### Custom domain on Cloudflare

1. Go to **Cloudflare Dashboard > Pages > gridops > Custom domains**.
2. Add your domain (e.g. `gridops.app` or a subdomain like `cf.gridops.app`).
3. Cloudflare will configure DNS automatically if the domain is already on Cloudflare. Otherwise, add the CNAME record it provides.

### Notes

- Cloudflare Pages deploys are independent of GitHub Pages. You can use both simultaneously (e.g. production on GitHub Pages, staging on Cloudflare Pages).
- Each `wrangler pages deploy` creates a new deployment with a unique preview URL. The production URL updates only when deploying to the production branch.
