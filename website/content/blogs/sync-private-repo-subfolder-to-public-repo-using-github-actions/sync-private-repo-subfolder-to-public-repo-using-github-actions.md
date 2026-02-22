# 🔄 Sync a Private Repo Subfolder to a Public Repo Using GitHub Actions 🚀

This guide walks you through everything from scratch 🏗️ — creating both repos, setting up permissions 🔐, and automating the sync ⚙️ so any change (including deletions 🗑️) in your private repo's subfolder automatically reflects in your public repo 🌍.

---

## 🏗️ What We Are Building

```
Private Repo (obsidian-data) 🔒
└── website/                ← only this folder gets synced 🔁
    ├── index.html
    ├── style.css
    └── blogs/
        └── post.md

Public Repo (website-content) 🌐
├── index.html              ← mirrors website/ folder exactly 🪞
├── style.css
└── blogs/
    └── post.md
```

Whenever you push a change ⬆️ to the `website/` folder in your private repo, GitHub Actions 🤖 will automatically copy those changes to your public repo — including deletions 🗑️.

---

## 🥇 Step 1: Create the Private Repo (Repo A) 🔒

1. Go to github.com 🌐 and log in 🔑
    
2. Click the **+** icon ➕ at the top right → **New repository**
    
3. Fill in:
    
    - **Repository name:** `obsidian-data`
        
    - **Visibility:** Select **Private** 🔒
        
    - Check **Add a README file** 📄
        
4. Click **Create repository** ✅
    

Now create the `website` folder inside it 📁:

1. Click **Add file** ➕ → **Create new file**
    
2. In the filename box type: `website/readme.md`
    
    - Typing the `/` automatically creates the folder 📂
        
3. Add any content like `# My Website` ✍️
    
4. Click **Commit new file** 💾
    

---

## 🥈 Step 2: Create the Public Repo (Repo B) 🌐

1. Click **+** ➕ → **New repository**
    
2. Fill in:
    
    - **Repository name:** `website-content`
        
    - **Visibility:** Select **Public** 🌍
        
    - Check **Add a README file** 📄
        
3. Click **Create repository** ✅
    

> ⚠️ The public repo must have at least one commit (not be completely empty) otherwise the sync will fail ❌. The README file handles this.

---

## 🔐 Step 3: Create a Personal Access Token (PAT)

The GitHub Action 🤖 in your private repo needs permission to push to your public repo. You give it this permission through a Personal Access Token 🎟️.

1. Click your **profile picture** 👤 (top right)
    
2. Click **Settings** ⚙️
    
3. Scroll down → click **Developer settings** 🛠️
    
4. Click **Personal access tokens** → **Tokens (classic)**
    
5. Click **Generate new token** → **Generate new token (classic)**
    
6. Fill in:
    
    - **Note:** `sync-obsidian-to-website` 🏷️
        
    - **Expiration:** Choose `No expiration` or set a date 📅
        
    - **Scopes:** Check `repo` (full repo read/write access) 🔓
        
7. Click **Generate token** ✅
    
8. **COPY THE TOKEN NOW** 📋 — you will not see it again.
    

---

## 🔑 Step 4: Add the Token as a Secret in Repo A

You store the token as a secret 🤫 so GitHub Actions can use it securely without exposing it.

1. Go to your **private repo** (`obsidian-data`) 🔒
    
2. Click the **Settings** tab ⚙️
    
3. Click **Secrets and variables** → **Actions** 🔐
    
4. Click **New repository secret** ➕
    
5. Fill in:
    
    - **Name:** `SYNC_TOKEN`
        
    - **Secret:** Paste the token 📋
        
6. Click **Add secret** ✅
    

---

## 🤖 Step 5: Create the GitHub Actions Workflow

This is the automation file ⚙️ that does the actual syncing 🔁.

1. Go to your **private repo** (`obsidian-data`)
    
2. Click **Add file** ➕ → **Create new file**
    
3. Type: `.github/workflows/sync.yml` 📁
    
4. Paste the workflow content

```
name: Sync website to public repo

on:
  push:
    paths:
      - 'website/**'

jobs:
  sync:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repo A
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Clone Repo B
        env:
          TOKEN: ${{ secrets.SYNC_TOKEN }}
        run: |
          git config --global credential.helper store
          echo "https://YOUR_GITHUB_USERNAME:${TOKEN}@github.com" > ~/.git-credentials
          git clone https://github.com/YOUR_GITHUB_USERNAME/website-content.git repo-b

      - name: Sync website folder (with deletes)
        run: |
          rsync -av --delete --exclude='.git' website/ repo-b/

      - name: Commit and push to Repo B
        run: |
          cd repo-b
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add -A
          git diff --cached --quiet || git commit -m "Sync from obsidian-data: ${{ github.sha }}"
          git push
```

1. Replace `YOUR_GITHUB_USERNAME` with your actual username 👤
    
2. Click **Commit new file** 💾
    

---

## Step 6: Understanding the Workflow (What Each Part Does)

```yaml
on:
  push:
    paths:
      - 'website/**'
```

This means the workflow only runs when a file inside the `website/` folder is changed. It won't trigger for changes outside that folder.

---

```yaml
git config --global credential.helper store
echo "https://YOUR_USERNAME:${TOKEN}@github.com" > ~/.git-credentials
```

This stores your token in git's credential system so all git operations (clone, push) use your token automatically.

---

```yaml
git clone https://github.com/YOUR_USERNAME/website-content.git repo-b
```

Downloads your public repo into a local folder called `repo-b` on the Actions runner machine.

---

```yaml
rsync -av --delete --exclude='.git' website/ repo-b/
```

Copies everything from your `website/` folder into `repo-b/`. The `--delete` flag is what makes deletions sync — if you delete a file in `website/`, rsync removes it from `repo-b/` too.

---

```yaml
git diff --cached --quiet || git commit -m "..."
```

This is a safety check — if there are no changes, it skips the commit so the workflow doesn't fail unnecessarily.

---

## Step 7: Work Locally on Your Computer

You can clone the private repo to your computer, make changes to files inside the `website/` folder, and push to GitHub as you normally would. Every time you push, GitHub Actions will automatically sync those changes to the public repo `website-content` — including any files you deleted.

---

## 🧪 Step 8: Test That It Works

Add a file ➕ → Watch Actions run 🤖 → See it in public repo 🌐.

Delete a file 🗑️ → Watch Actions run → Confirm removal ✅.

---

## 📂 Step 10: Folder Structure

```
obsidian-data/ 🔒
├── .github/
│   └── workflows/
│       └── sync.yml ⚙️
├── website/ 🔁
│   ├── index.html
│   └── blogs/
│       └── post.md
└── other-private-stuff/ 🔐
    └── notes.md
```

Only `website/` syncs 🔄. Everything else stays private 🔒.

---

## 🛠️ Troubleshooting

**Workflow not appearing?** ❓

- Make sure `sync.yml` is on `main` branch 🌿
    
- Make sure changes were inside `website/` 📂
    

**403 Permission denied?** 🚫

- Regenerate PAT with `repo` scope 🔓
    
- Check username spelling 🔎
    
- Re-add `SYNC_TOKEN` 🔁
    

**Files in wrong folder?** 📁

- Ensure `website/ repo-b/` has correct trailing slashes ✂️
    

**Nothing to commit?** ℹ️

- Means everything is already synced ✅.
    

---
