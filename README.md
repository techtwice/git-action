# git-action

A GitHub Actions project that automatically uploads the `ApkFile` folder to Google Drive — triggered on every push to `main` or manually via the Actions tab.

---

## How It Works

The workflow (`.github/workflows/upload-apk-to-drive.yml`) runs when:

- You **push** any commit to the `main` branch
- You **manually trigger** it from the GitHub Actions tab (`workflow_dispatch`)

It uses [rclone](https://rclone.org/) with a **Google Service Account** to securely upload the contents of the `ApkFile/` folder to a specified Google Drive folder.

---

## One-Time Setup

### Step 1 — Create a Google Cloud Project & Service Account

1. Go to [https://console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project (or use an existing one)
3. Enable the **Google Drive API**:
   - Navigate to **APIs & Services → Library**
   - Search for **Google Drive API** and click **Enable**
4. Create a **Service Account**:
   - Go to **APIs & Services → Credentials → Create Credentials → Service Account**
   - Give it a name (e.g., `github-actions-uploader`) and click **Done**
5. Open the Service Account, go to the **Keys** tab, click **Add Key → Create new key → JSON**
6. Download the `.json` key file — keep it safe

### Step 2 — Share a Google Drive Folder with the Service Account

1. In Google Drive, create (or choose) the folder where APKs should be uploaded
2. Right-click the folder → **Share**
3. Paste the Service Account email (found in the JSON file as `"client_email"`) and give it **Editor** access
4. Get the **Folder ID** from the folder's URL:
   ```
   https://drive.google.com/drive/folders/<FOLDER_ID_IS_HERE>
   ```

### Step 3 — Add GitHub Secrets

In your GitHub repo: **Settings → Secrets and variables → Actions → New repository secret**

| Secret Name                   | Value                                         |
|-------------------------------|-----------------------------------------------|
| `GDRIVE_SERVICE_ACCOUNT_KEY`  | Paste the **entire content** of the JSON key file |
| `GDRIVE_FOLDER_ID`            | The Google Drive folder ID from Step 2        |

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
