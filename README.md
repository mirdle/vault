# Vault — personal password keeper

One page (`index.html`) that runs in any browser, on desktop and phone.

## How it keeps your passwords private

- Everything is encrypted **on your device** (AES-256-GCM, with a key derived from your master password using PBKDF2-SHA256 at 600,000 rounds) before it leaves the device.
- The encrypted vault is saved as `vault.json` in a **private** GitHub repo. GitHub only ever holds ciphertext.
- The app itself sits in a separate **public** repo and is served by GitHub Pages. It contains no secrets, so it's fine for it to be public.
- To get into your vault, someone would need **both** your GitHub token (stored on your devices, encrypted with your master password) **and** your master password.
- The page can only talk to `api.github.com`. A Content-Security-Policy blocks every other network destination.
- It locks after 5 minutes idle, or after 1 minute in the background. Copied passwords are cleared from the clipboard after 30 seconds.

**If you forget your master password, nobody can recover your passwords, including you.** Write it down and keep it somewhere physically safe.

## One-time setup (about 10 minutes, all on github.com)

### 1. Create the private data repo
1. Go to https://github.com/new
2. Name: `vault-data`. Choose **Private**. Tick **Add a README file**, because the repo needs at least one commit.
3. Click **Create repository**.

### 2. Create the public app repo and turn on Pages
1. Go to https://github.com/new again.
2. Name: anything you like, e.g. `vault`. Choose **Public**. Tick **Add a README file**. Create it.
3. In the new repo, click **Add file → Upload files** and upload `index.html` from this folder. Commit.
4. Go to **Settings → Pages**. Under *Build and deployment*, set Source = **Deploy from a branch**, Branch = **main**, folder **/(root)**. Save.
5. After about a minute your app is live at `https://<your-username>.github.io/vault/`.

### 3. Create an access token that can only touch the vault repo
1. Go to https://github.com/settings/personal-access-tokens/new (this makes a *fine-grained* token).
2. Name: `vault`. Expiration: pick the longest allowed (you'll make a new one when it expires).
3. **Repository access → Only select repositories → `vault-data`.**
4. **Permissions → Repository permissions → Contents → Read and write.** Leave everything else alone.
5. Generate it and copy the `github_pat_...` value. You'll paste it once per device.

### 4. Connect each device
On your computer, and then on your phone:
1. Open `https://<your-username>.github.io/vault/`
2. Enter your GitHub username, `vault-data`, the token, and your master password.
   - On the first device this creates the vault (type the master password in both boxes).
   - On every later device it just unlocks the existing vault.
3. On your phone, use **Share → Add to Home Screen** (iPhone, in Safari) or **⋮ → Add to Home screen** (Android, in Chrome) so it opens like an app.

After that you only need your master password on each device.

## Day to day
- **+** adds an entry and generates a strong password. You can change the length and character types.
- Tap an entry to show, copy, edit or delete it.
- Changes sync through GitHub. If two devices save at about the same time, the entries are merged.
- With no signal it opens your last-synced copy, read-only.
- ☰ → **Download encrypted backup** saves a copy of the encrypted file. It still needs your master password to open.
- ☰ → **Change master password** re-encrypts the vault. The next time you unlock your other devices, they ask for the old password and then the new one.

## When the token expires
Make a new token (step 3), then on each device choose **Forget this device** and connect again with the new token.
