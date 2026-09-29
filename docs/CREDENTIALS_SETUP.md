# Credentials Setup Guide

This workflow requires two distinct credentials configured inside your n8n instance:
1. **Gmail OAuth2 API** (for Gmail polling, label management, labeling, and trashing)
2. **Header Auth** (for NVIDIA NIM chat completions)

> [!IMPORTANT]
> All credentials must be created directly within your own n8n instance under **Credentials > Add Credential**. Never commit credentials or API keys into git repositories.

---

## 1. Gmail OAuth2 API Credential

The workflow interacts with Gmail messages and labels. All Gmail operations utilize standard OAuth2.

### Step 1: Create a Google Cloud Project & Enable Gmail API
1. Navigate to the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a new project (e.g., `n8n-email-automation`).
3. In the sidebar, navigate to **APIs & Services > Library**.
4. Search for **Gmail API** and click **Enable**.

### Step 2: Configure OAuth Consent Screen
1. Go to **APIs & Services > OAuth consent screen**.
2. Select **External** (or **Internal** if using Google Workspace within an organization) and click **Create**.
3. Fill in basic application details (App name: `n8n Email Automation`, User support email, Developer contact email).
4. In the **Scopes** step, click **Add or Remove Scopes** and add:
   - `https://www.googleapis.com/auth/gmail.modify`
5. In the **Test users** step, add the Gmail address you plan to connect to the workflow.
6. Save and finish.

### Step 3: Create OAuth Client Credentials
1. Go to **APIs & Services > Credentials**.
2. Click **Create Credentials > OAuth client ID**.
3. Application type: **Web application**.
4. Name: `n8n Gmail OAuth Client`.
5. Under **Authorized redirect URIs**, enter your n8n OAuth callback URL:
   - For n8n Cloud: provided in the n8n credential modal.
   - For Self-Hosted: `https://<your-n8n-instance-domain>/rest/oauth2-credential/callback`
6. Click **Create** and copy your **Client ID** and **Client Secret**.

### Step 4: Configure the Credential in n8n
1. In your n8n dashboard, open **Credentials** from the left navigation and click **Add Credential**.
2. Search for **Gmail OAuth2 API**.
3. Enter your **Client ID** and **Client Secret**.
4. Set Scope to include: `https://www.googleapis.com/auth/gmail.modify`.
5. Click **Sign in with Google** and complete the OAuth authorization.
6. Save the credential as `Gmail OAuth2 Account`.

### Step 5: Attach to Workflow Nodes
Open the workflow and select your created credential on each of the following 6 nodes:
- `Gmail Trigger`
- `Get Labels`
- `Get Label`
- `Create Label`
- `Label Email`
- `Trash Email`

---

## 2. NVIDIA NIM Header Auth Credential

The classification engine uses NVIDIA's hosted inference API via HTTP Header Authentication.

### Step 1: Obtain an NVIDIA API Key
1. Visit [NVIDIA API Catalog](https://build.nvidia.com/).
2. Sign in or create a developer account.
3. Locate the model `nvidia/nemotron-3-ultra-550b-a55b` (or your chosen model).
4. Click **Get API Key** and generate an API key (`nvapi-...`).
5. Copy your API key securely.

### Step 2: Configure Header Auth Credential in n8n
1. In n8n, navigate to **Credentials > Add Credential**.
2. Search for **Header Auth**.
3. Configure the credential fields:
   - **Name**: `Authorization`
   - **Value**: `Bearer <YOUR_NVIDIA_API_KEY>` (replace `<YOUR_NVIDIA_API_KEY>` with your actual token).
4. Save the credential as `NVIDIA Header Auth`.

### Step 3: Attach to Classifier Node
1. In the workflow canvas, double-click the **Classifier** node.
2. Under **Credential for Header Auth**, select your `NVIDIA Header Auth` credential.
3. Save the workflow.

---

## Summary of Node Credential Attachments

| Node Name | Node Type | Credential Type | Purpose |
|---|---|---|---|
| **Gmail Trigger** | `n8n-nodes-base.gmailTrigger` | Gmail OAuth2 | Polls for new unread messages |
| **Get Labels** | `n8n-nodes-base.gmail` | Gmail OAuth2 | Queries live labels list |
| **Get Label** | `n8n-nodes-base.gmail` | Gmail OAuth2 | Queries specific label metadata |
| **Create Label** | `n8n-nodes-base.httpRequest` | Gmail OAuth2 (Predefined) | Creates missing label via Gmail REST API |
| **Classifier** | `n8n-nodes-base.httpRequest` | Header Auth | Dispatches prompt to NVIDIA API |
| **Label Email** | `n8n-nodes-base.gmail` | Gmail OAuth2 | Applies label ID to email message |
| **Trash Email** | `n8n-nodes-base.httpRequest` | Gmail OAuth2 (Predefined) | Moves message to Trash folder |
