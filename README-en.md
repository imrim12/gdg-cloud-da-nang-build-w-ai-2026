# GDG Cloud Codelab: Automate with Gemini CLI & Google Workspace CLI

**Language / Ngôn ngữ:** English (this file) · [Tiếng Việt → README.md](README.md)

---

Workshop guide for **Build with AI**, combining the **Gemini CLI** (language model) and **Google Workspace CLI (`gws`)** to automate Gmail, Drive, Calendar, and other Workspace workflows.

**Goal:** Set up your environment, authenticate safely, and practice `gws` → `gemini` pipelines using the shell pipe (`|`).

---

## Contents

1. [System requirements](#1-system-requirements)
2. [Install the CLIs](#2-install-the-clis)
3. [Workspace CLI: project, OAuth, and sign-in](#3-workspace-cli-project-oauth-and-sign-in)
4. [Verify the gws connection](#4-verify-the-gws-connection)
5. [Gemini CLI: API key and sign-in](#5-gemini-cli-api-key-and-sign-in)
6. [Hands-on labs](#6-hands-on-labs)
7. [Submission](#7-submission)

---

## 1. System requirements

Use this section on a clean machine or when you need to confirm versions.

### 1.1. Node.js (required)

Both `gemini` and `gws` run on Node.js.

**Check your version**

- **Windows:** open PowerShell and run:

```bash
node -v
```

- **macOS / Linux:** open Terminal and run:

```bash
node -v
```

If you see a version string (for example `v20.x.x`), Node.js is installed.

**Install (choose one)**

- **Option A — pnpm (recommended if you use pnpm)**

  - Windows (PowerShell):

```powershell
Invoke-WebRequest https://get.pnpm.io/install.ps1 -UseBasicParsing | Invoke-Expression
pnpm env use --global lts
```

  - macOS / Linux:

```bash
curl -fsSL https://get.pnpm.io/install.sh | sh -
pnpm env use --global lts
```

- **Option B — official installer:** download the **LTS** build from [https://nodejs.org/en/download](https://nodejs.org/en/download) and install using the instructions on that page.

### 1.2. Google Cloud CLI (`gcloud`)

Used to work with Google Cloud (projects, APIs, OAuth) in the `gws` flow.

1. Follow [https://docs.cloud.google.com/sdk/docs/install-sdk](https://docs.cloud.google.com/sdk/docs/install-sdk) for your operating system.
2. Verify the install:

```bash
gcloud --version
```

---

## 2. Install the CLIs

In Terminal / PowerShell (with Node.js and `npm` available):

```bash
npm install -g @google/gemini-cli
npm install -g @googleworkspace/cli
```

---

## 3. Workspace CLI: project, OAuth, and sign-in

On the **same Google Cloud project**, complete the steps below in order. This sequence avoids **403** errors while the OAuth app is in **Testing** mode (for example, finishing browser login before a user is added as a test user).

### Step 3.1. `gws` setup wizard (`gws auth setup`)

```bash
gws auth setup
```

Use the arrow keys and **Enter**:

| Wizard step | Action |
|-------------|--------|
| 1–2 | The tool checks `gcloud`; sign in to GCP if asked. |
| 3 (GCP project) | **Create new project** and name it (for example `gdg-cloud-codelab-<your-name>`), or select an existing project. |
| 4 (Workspace APIs) | Enable required APIs; **Space** to add Docs, Slides, Forms, Chat if needed → **Enter** to enable — wait until it finishes. |
| 5 (OAuth credentials) | When you see something like `Setup complete! Starting 'gws auth login'...`, **stop** — go to **Step 3.2** and add test users **before** you complete OAuth in the browser. |

### Step 3.2. Add test users (avoid HTTP 403)

In **Testing** mode, only Gmail addresses listed under **Test users** may complete OAuth.

1. Open [Google Cloud Console](https://console.cloud.google.com/) and select the **same project** used in `gws auth setup`.
2. **Google Auth Platform** (or **APIs & Services** → **OAuth consent screen**) → **Audience** / **Test users**.
3. **Add users** → enter the **exact** Gmail you will use for `gws auth login` → save.
4. Confirm it appears in the list.

### Step 3.3. Sign in to Workspace CLI

```bash
gws auth login
```

The browser opens — follow this order:

1. Pick the account that matches your test user (Step 3.2).
2. **Google hasn't verified this app** is expected for dev / internal clients → **Continue** (or **Advanced** → **Continue**).
3. Consent screen: grant the scopes you need (workshops often use **Select all**) → **Continue**.
4. In the terminal, JSON with `"status": "success"` means sign-in succeeded.

---

## 4. Verify the gws connection

```bash
gws drive files list
```

If you get output (often JSON) **without** a permission error — even an empty list — `gws` is working. Then continue to **Section 5** to configure Gemini for labs that use a pipe (`|`).

---

## 5. Gemini CLI: API key and sign-in

Gemini CLI uses an **API key** from Google AI Studio; this does **not** replace OAuth for `gws` in Section 3. Complete both before the hands-on labs.

| Step | Action |
|------|--------|
| 1 | Open [Google AI Studio — API keys](https://aistudio.google.com/app/apikey), sign in, **Create API key**, and copy it. |
| 2 | In a terminal, run `gemini --yolo` (or `gemini` if you do not want quick-approval / “yolo” behavior in the CLI). |
| 3 | At the Gemini CLI prompt, enter the slash command: `/auth signin`. |
| 4 | Choose **Use Gemini API Key**, paste your key. When done, you may exit (`/exit` or `Ctrl+C`) if this was a one-time setup. |

```bash
gemini --yolo
```

```text
/auth signin
```

---

## 6. Hands-on labs

The examples use the pipe (`|`) to stream JSON from `gws` into `gemini`.

### Lab 1 — Analyze a Drive file listing

**Scenario:** List files from Drive and ask the model for keep/delete suggestions from names and JSON metadata.

```bash
gws drive files list --params '{"pageSize": 20}' --format json | gemini "This is my Google Drive file list as JSON. Analyze it and return a Markdown table with: file name, size, and your suggestion (keep or delete based on whether the name looks important or like clutter)."
```

### Lab 2 — Today’s calendar + creative lines

**Scenario:** Read today’s Calendar agenda and generate a short poem or encouraging line per event.

```bash
gws calendar +agenda --today --format json | gemini "This is my calendar for today. List each meeting, and for each one write either a fun two-line poem or a short encouraging note that fits the meeting title."
```

### Lab 3 — Summarize promotional email

**Scenario:** Pull a few Promotions messages and extract brand, discount, and promo codes (if any).

```bash
gws gmail +triage --max 5 --query "category:promotions" --format json | gemini "Read these promotional emails. Extract only: brand name, discount level, and promo code if present. Format as a clear table."
```

### Lab 4 — Draft CS email, send via Gmail

**Step 1** — Generate body text with Gemini and save to a file:

```bash
echo "Customer: Anh Tu. Product: mechanical keyboard. They complained shipping was slow." | gemini "You are support staff. Write a short email apologizing for slow shipping, thanking them for buying the mechanical keyboard, and include discount code GIA50. Output only the email body, nothing else." > email_body.txt
```

**Step 2** — Send (change recipient and subject to match your scenario):

```bash
gws gmail +send --to "tu.nguyen@example.com" --subject "Sorry and thanks from Shop GDG" --body "$(cat email_body.txt)"
```

---

## 7. Submission

Organizers provide a public Google Sheet. After you finish the codelab, append your row with the CLI.

**Include:** Your name, short feedback on the codelab, and (if applicable) the poem or quote from Lab 2.

Replace `SPREADSHEET_ID_FROM_MC` with the ID announced by the host:

```bash
gws sheets +append --spreadsheet "SPREADSHEET_ID_FROM_MC" --values "Your name, Short feedback, Lab 2 output here"
```

---

*Material for GDG Cloud Da Nang — Build with AI.*
