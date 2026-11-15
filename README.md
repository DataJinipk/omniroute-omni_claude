# OmniRoute – Omni_Claude Combo

## Professional Overview
This repository provides a production‑grade, reusable configuration for **OmniRoute** that enables the **Omni_Claude** model combo. The combo automatically selects a free Claude model and gracefully falls back to a paid tier when required (e.g., quota exhaustion or rate‑limit errors). The repository is deliberately minimal, containing only the essential artifacts to bootstrap a secure, reproducible environment.

### Why this repo?
- **Security‑first**: No secret keys are stored in version control. The required `.env` file is ignored via `.gitignore` and contains placeholders that must be filled locally.
- **Portability**: Clone the repo on any machine, install OmniRoute, and you have a ready‑to‑use combo without manual UI steps.
- **Extensibility**: The `config/omni_claude_combo.json` file can be edited to adjust fallback order, add new providers, or change context limits.

---

## Repository Structure
```
omniroute/                     # repository root
│   .gitignore                # excludes runtime artefacts and .env
│   README.md                 # this professional guide
│
└─ config/
    │   omni_claude_combo.json   # Omni_Claude combo definition (JSON)
    │   placeholder.txt          # placeholder file (optional artifact)
```

---

## Prerequisites
1. **Node.js** (v20 or newer) – install from https://nodejs.org.
2. **OmniRoute** – global npm installation:
   ```powershell
   npm i -g omniroute
   ```
3. **Git** – command‑line tools for version control.
4. **An OmniRoute API key** – keep this confidential (e.g., `sk‑f…`).
5. (Optional) **GitHub CLI** (`gh`) for repo creation.

---

## Step‑by‑Step Setup
### 0️⃣ Create the remote repository (if not already present)
```powershell
# Using GitHub CLI (requires prior authentication)
gh repo create datajinipk/omniroute-omni_claude --public --confirm
```
Or create the repository manually on GitHub and copy the HTTPS clone URL.

### 1️⃣ Clone the repository locally
```powershell
cd $HOME\.gemini\antigravity\scratch   # or any preferred workspace
git clone https://github.com/datajinipk/omniroute-omni_claude.git
cd omniroute-omni_claude
```

### 2️⃣ Configure local secrets – **do not commit**
Create a `.env` file at the repository root with the following placeholders (the file is already ignored by `.gitignore`):
```powershell
@'
STORAGE_ENCRYPTION_KEY=<YOUR_SE_KEY>   # NOTE: replace with your actual key (do NOT commit)
REQUIRE_API_KEY=true
ANTHROPIC_AUTH_TOKEN=<YOUR_API_KEY>   # <-- replace with your real key (do NOT commit)
'@ | Set-Content -Path .env -Encoding UTF8
```
> **Security Note** – `.env` must never be added to the repository. The `.gitignore` entry guarantees it stays local.

### 3️⃣ Verify the ignore configuration
```powershell
git status   # .env should appear under “Untracked files” only
```
If `.env` is staged, run:
```powershell
git rm --cached .env
git commit -m "Remove .env from tracking"
```

### 4️⃣ Launch OmniRoute with the combo loaded
1. **Start the server in daemon mode** (keeps running in the background):
   ```powershell
   omniroute serve --daemon
   ```
   You will see output similar to:
   - Dashboard: `http://localhost:20128`
   - API Base: `http://localhost:20128/v1`
2. **Import the combo** via the web UI:
   - Open a browser and navigate to `http://localhost:20128`.
   - In the sidebar choose **Combos → Import**.
   - Paste the contents of `config/omni_claude_combo.json` and click **Import**.
   - The combo `Omni_Claude` will now be listed and ready for use.

### 5️⃣ Connect a client (e.g., DeepSeek Harness)
In the client’s **Custom Provider** settings populate:
- **Base URL**: `http://localhost:20128`
- **Model**: `Omni_Claude`
- **API Key**: the same value you set for `ANTHROPIC_AUTH_TOKEN` in `.env`
Test a simple prompt to confirm the pipeline works without quota errors.

---

## Quality Review Checklist
- [ ] **Security** – Verify `.env` is not tracked (`git ls-files .env` returns nothing).
- [ ] **Combo Import** – Confirm `Omni_Claude` appears in the OmniRoute dashboard after import.
- [ ] **Client Test** – Send a prompt through DeepSeek Harness and ensure a response is received.
- [ ] **Documentation** – All placeholders (`<YOUR_SE_KEY>`, `<YOUR_API_KEY>`) are clearly marked and not real values.
- [ ] **Version Control** – Repository contains only `.gitignore`, `README.md`, and the JSON combo definition.

---

## Maintaining the Repository
- **Updating the combo**: Edit `config/omni_claude_combo.json` and commit the change.
  ```powershell
  git add config/omni_claude_combo.json
  git commit -m "Update Omni_Claude combo configuration"
  git push
  ```
- **Branching**: Use feature branches for extensive modifications and open a Pull Request for review.
- **Release Tags**: Tag stable releases (`v1.0.0`, `v1.1.0`, …) to freeze a known‑good configuration.

---

## License
MIT – feel free to fork, adapt, and integrate into your own projects.
