# OmniRoute – Omni_Claude Combo

## Overview
This repository provides a minimal, reusable setup for **OmniRoute** with the **Omni_Claude** model combo. It contains:
- A JSON definition of the `Omni_Claude` combo (free model with a paid fallback).
- A `.gitignore` that excludes all runtime artefacts and the secret `.env` file.
- A concise guide on how to clone the repo, configure OmniRoute locally, and use the combo in DeepSeek Harness (or any OpenAI‑compatible client).

**Important:** The API key is **not** stored in the repository. It must be placed in a local `.env` file that is never committed.

---

## Repository structure
```
omniroute/                     # repository root
│   .gitignore                # excludes runtime files and .env
│   README.md                 # this guide (you are reading it)
│
└─ config/
    │   omni_claude_combo.json   # Omni_Claude combo definition (import via dashboard)
```

---

## Prerequisites
1. **Node.js** (v20 or newer) installed.
2. **OmniRoute** installed globally:
   ```powershell
   npm i -g omniroute
   ```
3. A valid OmniRoute **API key** (e.g., `sk‑f…`). Keep this key private.
4. **Git** installed.
5. (Optional) **GitHub CLI** (`gh`) for creating the remote repo.

---

## Step‑by‑step setup
### 0. (If needed) Create the GitHub repository
```powershell
# Using GitHub CLI (requires you to be logged in)
gh repo create datajinipk/omniroute-omni_claude --public --confirm
```
Or create it manually on https://github.com and copy the HTTPS URL.

### 1. Clone the repository
```powershell
# Choose where you want the repo
cd $HOME\.gemini\antigravity\scratch   # or any folder you prefer
git clone https://github.com/datajinipk/omniroute-omni_claude.git
cd omniroute-omni_claude
```
If the repository does not yet exist on GitHub, perform step 0 first.

### 2. Add your secret to a local `.env` (do **not** commit!)
```powershell
@'
STORAGE_ENCRYPTION_KEY=b81f3a27345449b216c4354d90307f7c05df56a14b7fb279b21d684ba55f28bb
REQUIRE_API_KEY=true
ANTHROPIC_AUTH_TOKEN=sk-f56178d215459c60-025cd1-1bde94f3   # <-- your real key
'@ | Set-Content -Path .env -Encoding UTF8
```
The line above adds the key **only to your local copy**; `.gitignore` already prevents it from being tracked.

### 3. Verify `.gitignore` protects the key
```powershell
git status   # .env should appear under “Untracked files”
```
If `.env` appears under “Changes to be committed”, run:
```powershell
git rm --cached .env
git commit -m "Remove .env from tracking"
```

### 4. Start OmniRoute with the combo loaded
1. **Launch OmniRoute** (daemon mode so it stays running):
   ```powershell
   omniroute serve --daemon
   ```
   The output will show:
   - Dashboard: `http://localhost:20128`
   - API Base: `http://localhost:20128/v1`
2. **Import the combo** via the web UI:
   - Open a browser and go to `http://localhost:20128`.
   - In the left sidebar select **Combos → Import**.
   - Paste the contents of `config/omni_claude_combo.json` and click **Import**.
   - The combo `Omni_Claude` will now appear in the list.

### 5. Configure a client (e.g., DeepSeek Harness)
In the client’s custom‑provider settings use:
- **Base URL**: `http://localhost:20128`
- **API Key**: the same key you placed in `.env` (the client needs it for authentication).
- **Model**: `Omni_Claude`
Now any prompt will be routed through OmniRoute, using the free model first and falling back to the paid model if needed.

---

## Updating the combo
If you need to change the fallback order or add new models:
1. Edit `config/omni_claude_combo.json` locally.
2. In the OmniRoute dashboard go to **Combos → Edit** and replace the JSON.
3. Commit the change if you want to version‑control it:
   ```powershell
   git add config/omni_claude_combo.json
   git commit -m "Update Omni_Claude combo"
   git push
   ```

---

## License
MIT – feel free to fork, modify, and use in your own projects.
