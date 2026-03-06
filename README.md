# n8n + GitHub + Hostinger Setup

This project connects your **n8n** workflows and code to **GitHub** and deploys to **Hostinger** so everything stays live and in sync.

## 1. Connect this project to GitHub

Run these in your terminal (replace with your GitHub repo URL):

```bash
cd "/Users/mpatil/Cursor Projects/N8N"
git init
git add .
git commit -m "Initial commit: n8n project"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git push -u origin main
```

**If you don’t have a repo yet:** Create one on [github.com/new](https://github.com/new), then use its URL in `git remote add origin ...`.

---

## 2. Connect GitHub to Hostinger

Hostinger can pull from GitHub in two main ways.

### Option A: Hostinger Git (hPanel) + Webhook (auto deploy)

1. In **Hostinger hPanel** go to **Advanced → Git**.
2. Click **Create Repository** and connect your GitHub repo (authorize if asked).
3. Choose the branch (e.g. `main`) and the folder on the server where n8n runs.
4. Copy the **Webhook URL** Hostinger shows.
5. On **GitHub**: repo → **Settings → Webhooks → Add webhook**:
   - **Payload URL:** paste the Hostinger webhook URL
   - **Content type:** `application/json`
   - **Events:** “Just the push event”
6. Save. Every `git push` to that branch will trigger a deploy on Hostinger.

### Option B: GitHub Actions + SSH (for VPS or SSH access)

If you have SSH access (e.g. Hostinger VPS):

1. In Hostinger: **Advanced → SSH Access** → add your **public** SSH key.
2. In this repo, create `.github/workflows/deploy.yml` (see below).
3. In GitHub: **Settings → Secrets and variables → Actions** → add:
   - `HOSTINGER_HOST` (e.g. `srv123.hostinger.com`)
   - `HOSTINGER_USER` (SSH username)
   - `HOSTINGER_SSH_KEY` (your **private** SSH key, full contents)
   - `HOSTINGER_DEPLOY_PATH` (e.g. `~/domains/yourdomain.com/public_html` or your n8n app path)
   - `HOSTINGER_PORT` (e.g. `65002` for shared hosting, `22` for VPS)

Then every push to `main` can run the workflow and deploy via SSH.

---

## 3. Workflow: Code → GitHub → Hostinger → n8n

1. **Edit** your n8n workflows or code in Cursor (this folder).
2. **Commit and push** to GitHub:
   ```bash
   git add .
   git commit -m "Describe your change"
   git push
   ```
3. **Deploy:**
   - With **Option A**: Hostinger auto-deploys when the webhook receives the push.
   - With **Option B**: GitHub Actions runs and deploys via SSH.
4. Your **n8n** instance on Hostinger uses the updated code/workflows.

---

## 4. What to put in this repo

- n8n **workflow JSON** exports (e.g. from n8n UI: workflow → ⋮ → Download).
- Any **custom nodes** or **scripts** your n8n instance uses.
- **Env/config templates** (e.g. `.env.example`) — never commit real `.env` or secrets.

Use the `.gitignore` in this repo so secrets and local data are not pushed.
