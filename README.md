# PRIME HOSTING v5.4 — Render Ready

## CRITICAL WARNINGS (read before deploying)

1. **Free Render Web Service = data loss + sleep.**  
   Filesystem is ephemeral. SQLite + hosted bot files are wiped on every restart/sleep.  
   Free tier also sleeps after 15 min → bot goes offline.

2. **Correct setup for zero data-loss:**  
   - Service type: **Background Worker** (not Web Service)  
   - Plan: **Starter** (~$7/month) or higher  
   - Attach **Persistent Disk** (mount path `/data`, size 1 GB+)  
   - Environment variables: `BOT_TOKEN`, `ADMIN_ID`, `DATA_DIR=/data`

3. This bot runs other user Python bots via subprocess + auto-pip.  
   512 MB free tier will struggle. Paid Starter is the minimum realistic plan.

4. Never commit real BOT_TOKEN / ADMIN_ID to Git. Use Render Environment Variables only.

---

## Quick deploy (mobile-friendly)

### 1. Create GitHub repo from this zip
- On phone: download the zip → extract → upload all files to a new private GitHub repo (GitHub mobile app or github.com).

### 2. Render dashboard
1. https://dashboard.render.com → New → Background Worker
2. Connect the GitHub repo
3. Settings:
   - Runtime: Python
   - Build Command: `pip install -r requirements.txt`
   - Start Command: `python bot.py`
   - Plan: Starter (or higher)
4. Advanced → Add Disk:
   - Name: prime-data
   - Mount Path: `/data`
   - Size: 1 GB (increase later if needed)
5. Environment Variables (Add):
   - `BOT_TOKEN` = your bot token from @BotFather
   - `ADMIN_ID` = your numeric Telegram user ID
   - `DATA_DIR` = `/data`
6. Create Worker → wait for deploy.

### 3. After first deploy
- Open Logs. You should see:
  ```
  👑 ƬʜᴇΉΛᑕKΣЯ♛
  ⚡ PRIME HOSTING SERVER v5.4 ...
  ✅ Bot Username: @yourbot
  🚀 Starting infinity_polling...
  ```
- Send `/start` to your bot.

### 4. Data persistence
- DB file: `/data/hosting_data.db`
- User bot files: `/data/hosted_files/<user_id>/`
- On restart / redeploy the disk keeps everything.  
  Crash-guard will mark old PIDs as dead and set status to stopped.  
  You (or users) can press Start again from My Bots. No file/DB loss.

---

## Local test (optional)
```bash
export BOT_TOKEN=123:ABC
export ADMIN_ID=5427735251
export DATA_DIR=./data
python bot.py
```

---

## Common errors & fixes

| Error | Cause | Fix |
|-------|-------|-----|
| BOT_TOKEN is required | Env var missing | Add BOT_TOKEN in Render → Environment |
| Unauthorized / 401 | Wrong or revoked token | New token from BotFather, update env |
| 409 Conflict | Another instance polling same token | Kill other instances / only one worker |
| Data gone after restart | No Disk or wrong mount path | Attach Disk at `/data` + DATA_DIR=/data |
| Bot sleeps | You used free Web Service | Switch to paid Background Worker |
| OOM / killed | Too many hosted bots on small plan | Upgrade plan or lower free/prime limits |

---

## Security notes
- Original file contained a hardcoded token. It has been removed.
- Always set secrets only in Render dashboard (never in code or Git).
- Force-join channel and admin checks still work as before.

