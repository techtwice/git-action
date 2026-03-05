# git-action

A GitHub Actions project that automatically uploads the `ApkFile` folder to Google Drive — triggered on every push to `main` or manually via the Actions tab.

---

## How It Works

The workflow (`.github/workflows/upload-apk-to-drive.yml`) runs when:

- You **push** any commit to the `main` branch
- You **manually trigger** it from the GitHub Actions tab (`workflow_dispatch`)

It uses [rclone](https://rclone.org/) to upload the contents of `ApkFile/` to Google Drive.

---

## Setup — Choose Your Account Type

> **Not sure which to use?**
> - **Personal Google account** (gmail.com) → use Option A
> - **Google Workspace** (company/school account with Shared Drives) → use Option B

---

## Option A — Personal Google Drive (OAuth Token)

Service accounts cannot upload to a personal Google Drive (no storage quota). Instead, rclone uses an **OAuth refresh token** that uploads as your own Google account using your personal 15 GB quota.

### Step 1 — Install rclone on your Mac

```bash
brew install rclone
```

### Step 2 — Generate an OAuth token

```bash
rclone authorize "drive"
```

- A browser window will open — log in with your Google account and click **Allow**
- rclone prints a JSON token in the terminal that looks like:
  ```
  {"access_token":"...","token_type":"Bearer","refresh_token":"...","expiry":"..."}
  ```
- **Copy the entire JSON block**

### Step 3 — Get your Google Drive Folder ID

Open the target folder in Google Drive. Copy the ID from the URL:
```
https://drive.google.com/drive/folders/<FOLDER_ID_IS_HERE>
```

### Step 4 — Add GitHub Secrets

Go to your repo → **Settings → Secrets and variables → Actions → New repository secret**

| Secret Name         | Value                                   |
|---------------------|-----------------------------------------|
| `GDRIVE_OAUTH_TOKEN` | The full JSON token from Step 2        |

> The folder ID is hardcoded in the workflow file directly (it is not sensitive).

### Workflow config used

```yaml
token = $GDRIVE_OAUTH_TOKEN
scope = drive
root_folder_id = <your-folder-id>
```

---

## Option B — Google Workspace (Service Account + Shared Drive)

Google Workspace accounts support **Shared Drives**, which have their own storage quota and fully support service accounts. Personal accounts do **not** support Shared Drives.

### Step 1 — Create a Google Cloud Project & Service Account

1. Go to [https://console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project (or use an existing one)
3. Enable the **Google Drive API**:
   - Navigate to **APIs & Services → Library → Google Drive API → Enable**
4. Create a **Service Account**:
   - Go to **APIs & Services → Credentials → Create Credentials → Service Account**
   - Give it a name (e.g., `github-actions-uploader`) and click **Done**
5. Open the Service Account → **Keys** tab → **Add Key → Create new key → JSON**
6. Download the `.json` key file

### Step 2 — Create a Shared Drive and add the Service Account

1. In Google Drive → left sidebar → **Shared drives → + New**
2. Open the Shared Drive → click its name → **Manage members**
3. Add the service account email (from `"client_email"` in the JSON) as **Content manager**
4. Copy the Shared Drive ID from its URL:
   ```
   https://drive.google.com/drive/u/0/folders/<SHARED_DRIVE_ID>
   ```

### Step 3 — Add GitHub Secrets

| Secret Name                    | Value                                          |
|--------------------------------|------------------------------------------------|
| `GDRIVE_SERVICE_ACCOUNT_KEY`   | Full content of the downloaded JSON key file   |
| `GDRIVE_TEAM_DRIVE_ID`         | The Shared Drive ID from Step 2                |

### Workflow config used

```yaml
service_account_file = /tmp/sa_key.json
team_drive = $GDRIVE_TEAM_DRIVE_ID
```

---

## Triggering Manually

1. Go to your repo on GitHub
2. Click the **Actions** tab
3. Select **Upload APK Folder to Google Drive**
4. Click **Run workflow → Run workflow**

---

## Project Structure

```
git-action/
├── .github/
│   └── workflows/
│       └── upload-apk-to-drive.yml   ← GitHub Actions workflow
├── ApkFile/                          ← Put your APK files here
├── index.html
└── README.md
```
