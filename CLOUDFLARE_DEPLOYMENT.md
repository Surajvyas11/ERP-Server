# Deploying ERPNext & Frontend on Cloudflare

This repository is configured to support deployment to **Cloudflare** via two primary methods:

1. **Option 1: Full ERPNext Deployment via Cloudflare Tunnel (`cloudflared`)**
   Deploy the full ERPNext stack (Python backend, MariaDB, Redis, SocketIO, frontend) on any VPS, cloud server, or local machine, and route public traffic securely through Cloudflare Zero Trust.
2. **Option 2: Standalone Banking Frontend on Cloudflare Pages**
   Build and deploy the React/Vite banking app to Cloudflare Pages via GitHub Actions.

---

## 🚀 Part 1: Initializing Git & Pushing to GitHub

To push this codebase to your own GitHub repository:

```bash
# 1. Initialize git and checkout the main branch
git init -b main

# 2. Add all files to staging
git add .

# 3. Create initial commit
git commit -m "feat: configure repository for GitHub and Cloudflare deployment"

# 4. Link your remote GitHub repository (replace with your repository URL)
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git

# 5. Push to GitHub
git push -u origin main
```

---

## 🌐 Option 1: Full ERPNext Deployment with Cloudflare Tunnel

Cloudflare Tunnel creates an encrypted outbound-only connection between your server and Cloudflare Edge. You do **not** need to open firewall ports or expose your server's public IP address.

### Step 1: Create a Cloudflare Tunnel
1. Log in to your [Cloudflare Dashboard](https://dash.cloudflare.com/).
2. Navigate to **Zero Trust** > **Networks** > **Tunnels**.
3. Click **Create a Tunnel** (select `Cloudflared`).
4. Name your tunnel (e.g., `erpnext-tunnel`).
5. Copy the generated **Tunnel Token**.
6. In Public Hostname settings, map your domain (e.g., `erp.yourdomain.com`) to:
   - **Service Type:** `HTTP`
   - **URL:** `frontend:80` (or `localhost:80` if running standalone)

### Step 2: Configure Environment Variables
Copy `.env.example` to `.env`:
```bash
cp .env.example .env
```
Fill in your `CLOUDFLARE_TUNNEL_TOKEN` and `MYSQL_ROOT_PASSWORD`.

### Step 3: Start the Docker Stack
```bash
docker compose -f docker-compose.cloudflare.yml up -d
```
Your ERPNext application will automatically be live and secured with Cloudflare SSL at `https://erp.yourdomain.com`.

---

## ⚡ Option 2: Deploy Banking Frontend to Cloudflare Pages

The [`banking`](file:///banking) directory contains a Vite + React application.

### Automatic Deployment via GitHub Actions
A workflow file has been created at [`.github/workflows/deploy-cloudflare.yml`](file:///.github/workflows/deploy-cloudflare.yml).

1. Go to your GitHub repository > **Settings** > **Secrets and variables** > **Actions**.
2. Add the following repository secrets:
   - `CLOUDFLARE_API_TOKEN`: Cloudflare API token with `Cloudflare Pages: Edit` permissions.
   - `CLOUDFLARE_ACCOUNT_ID`: Your Cloudflare Account ID (found on your dashboard).
3. Every time you push to the `main` branch, GitHub Actions will automatically build the banking frontend and deploy it to Cloudflare Pages.

### Manual / Dashboard Deployment on Cloudflare Pages
If connecting directly from the Cloudflare Dashboard:
1. In the Cloudflare Dashboard, go to **Workers & Pages** > **Create application** > **Pages** > **Connect to Git**.
2. Select your GitHub repository.
3. Configure build settings:
   - **Framework preset:** `Vite`
   - **Root directory:** `banking`
   - **Build command:** `yarn build:pages`
   - **Build output directory:** `dist`
4. Click **Save and Deploy**.
