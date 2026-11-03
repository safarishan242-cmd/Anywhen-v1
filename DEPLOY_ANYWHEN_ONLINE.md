# Publish Anywhen at https://anywhenai.online

This edition is ready for a **single-instance Render Web Service** with a persistent SQLite disk. It is built from Anywhen v2. The `render.yaml` file preconfigures production settings, HTTPS origin, the startup command, the health check, and persistent storage. It does **not** contain an API key. Do not upload a `.env` file to GitHub.

## 1. Put this code on GitHub

1. Extract `Anywhen-domain-deploy.zip`.
2. Visit https://github.com and sign in or create an account.
3. Click **+** (top-right) → **New repository**.
4. Name it `anywhenai`, choose **Private**, and click **Create repository**. Do not initialize with a README.
5. Choose **uploading an existing file**. From the *inside* of the extracted folder, upload the actual project files and folders (`package.json`, `render.yaml`, `.node-version`, `public`, `server`, `tests`). **Do not upload any `.env`, database (`.db`), or API key.**
6. Click **Commit changes**. Ensure `render.yaml` is visible at the root of the GitHub repository, alongside `package.json`.

Note: GitHub's browser uploader can accept dragged files/folders in supported browsers. If your browser won't upload folders, use GitHub Desktop to publish the extracted folder instead: https://desktop.github.com/.

## 2. Deploy with Render

1. Visit https://dashboard.render.com and sign up/sign in with GitHub.
2. Click **New** → **Blueprint**. Connect your private `anywhenai` repository.
3. Keep the file path `render.yaml`; accept the listed service `anywhenai`.
4. When asked for `ANTHROPIC_API_KEY`, paste a **new rotated key** from Anthropic Console. Never reuse the key pasted into chat. Render stores it as an environment secret.
5. Review pricing **before** you click deploy. The Blueprint specifies a **paid** 512 MB web service plus a **1 GB persistent disk**. At the time of writing, Render lists roughly US $7/month for the service and $0.25/GB/month for disk, plus Claude API requests and any other usage charges.
6. Deploy the Blueprint. In Render logs, look for `Anywhen running`.
7. Open your generated Render URL (such as `https://anywhenai.onrender.com`). It is okay to test there before DNS is connected; the server allows the exact Render-generated domain.
8. Test the welcome scene, **Continue observing**, and **Settings**. Open `/api/connection` on the Render site if you need to check Claude access.

## 3. Connect anywhenai.online

1. In Render, open the web service **anywhenai**.
2. Open **Settings** → **Custom Domains** → **Add Custom Domain**.
3. Enter `anywhenai.online`, and save. Render may also configure `www.anywhenai.online` and redirect it to the root.
4. Open the website where you manage DNS for `anywhenai.online` (the domain registrar/DNS host).
5. Add/replace DNS entries **exactly as Render displays**. Root (`@`) domain DNS normally uses a Render-specified `A` record, or ALIAS/CNAME flattening if supported. The `www` subdomain normally uses a `CNAME`. Do not guess an IP address; use Render's current values in its dashboard or documentation. Don't remove mail-related records (MX, SPF, DKIM, DMARC).
6. If old hosting records conflict, remove/replace only those web-serving records. Render instructs you to remove incompatible `AAAA` records for the site.
7. In Render, click **Verify**. After DNS propagation, visit https://anywhenai.online. Render handles HTTPS certificates.

Official DNS instructions: https://render.com/docs/custom-domains

## 4. Before inviting public users

- **Very important:** This is a functional prototype, not a security-audited public launch. Any visitor can initiate billable Claude generations. Session-based daily quotas can be bypassed by creating new guest sessions. Add a global usage budget and stronger abuse protection before public marketing.
- Set spend limits and usage notifications in your Anthropic account and review Render billing.
- Check that saved lives survive a redeploy (the disk at `/var/data` is essential).
- Backup the SQLite database regularly. The disk enables persistence but is not a substitute for regular backups.
- If your old local Anywhen database contains worlds you want on the website, it **won't automatically transfer**. A database migration/import is a separate task.
- Keep your domain registration active. DNS changes alone do not move the domain's ownership.

### Current configured environment

| Variable | Value |
| --- | --- |
| `APP_ORIGIN` | `https://anywhenai.online` |
| `DATABASE_PATH` | `/var/data/anywhen.db` |
| `ANTHROPIC_MODEL` | `claude-sonnet-5-5` |
| `NODE_ENV` | `production` |
| `ENABLE_WEB_SEARCH` | `1` |
| `DAILY_GENERATION_LIMIT` | `20` (per session/account; **not** a global spending cap) |
| `ANTHROPIC_API_KEY` | supplied privately at deploy time |

The app binds to the platform-assigned `PORT` and to `0.0.0.0` automatically.
