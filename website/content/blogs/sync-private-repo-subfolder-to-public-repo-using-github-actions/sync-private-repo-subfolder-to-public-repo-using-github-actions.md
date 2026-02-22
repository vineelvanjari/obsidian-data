# 🚀 Sync Private Repo Subfolder → Public Repo Automatically

> 🔄 Automatically mirror a subfolder from a private repository into a public repository using GitHub Actions — including deletions.

---

## 🏗 What We Are Building

```plaintext
🔒 Private Repo: obsidian-data
└── 📁 website/        ← Only this folder syncs
    ├── 📄 index.html
    ├── 🎨 style.css
    └── 📝 blogs/
        └── post.md

🌍 Public Repo: website-content
├── 📄 index.html      ← Exact mirror of website/
├── 🎨 style.css
└── 📝 blogs/
    └── post.md
```

🧠 **Goal:**  
Every push to `website/` automatically updates the public repo — even deleted files.

---

# 🧭 Step 1 — Create Private Repository

### 1️⃣ Create Repo

- Go to 👉 [https://github.com](https://github.com/)
- Click ➕ **New Repository**
- Name it: `obsidian-data`
- Visibility: 🔒 **Private**
- ✅ Add README
- Click **Create**
---

### 2️⃣ Create `website/` Folder

Click **Add file → Create new file**

Type:

```
website/readme.md
```

GitHub automatically creates the folder.

Add:

```
# 🌐 My Website
```

Commit ✅

---

# 🌍 Step 2 — Create Public Repository

- Click ➕ **New Repository**
    
- Name: `website-content`
    
- Visibility: 🌍 **Public**
    
- ✅ Add README
    
- Create
    

⚠️ Important: Public repo must NOT be empty.

---

# 🔑 Step 3 — Create Personal Access Token (PAT)

We need permission for automation.

### 📌 Steps

1. Click profile → **Settings**
    
2. Scroll → **Developer Settings**
    
3. Click **Personal Access Tokens (classic)**
    
4. Click **Generate new token**
    
5. Fill:
    
    - Note: `sync-obsidian-to-website`
        
    - Expiration: Your choice
        
    - ✅ Check `repo`
        
6. Click Generate
    

⚠️ COPY TOKEN IMMEDIATELY  
It starts with: `ghp_...`

---

# 🔐 Step 4 — Add Token as Secret

Go to:

Private repo → **Settings → Secrets and variables → Actions**

Click ➕ **New repository secret**

| Field  | Value            |
| ------ | ---------------- |
| Name   | `SYNC_TOKEN`     |
| Secret | Paste your token |

Save ✅

---

# ⚙️ Step 5 — Create GitHub Action Workflow

Create this file in private repo:

```plaintext
.github/workflows/sync.yml
```

Paste:

```yaml
name: 🚀 Sync website to public repo

on:
  push:
    paths:
      - 'website/**'

jobs:
  sync:
    runs-on: ubuntu-latest

    steps:
      - name: 📥 Checkout Repo A
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: 📦 Clone Repo B
        env:
          TOKEN: ${{ secrets.SYNC_TOKEN }}
        run: |
          git config --global credential.helper store
          echo "https://YOUR_GITHUB_USERNAME:${TOKEN}@github.com" > ~/.git-credentials
          git clone https://github.com/YOUR_GITHUB_USERNAME/website-content.git repo-b

      - name: 🔄 Sync website folder (with deletes)
        run: |
          rsync -av --delete --exclude='.git' website/ repo-b/

      - name: 🚀 Commit and push
        run: |
          cd repo-b
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add -A
          git diff --cached --quiet || git commit -m "Sync from obsidian-data: ${{ github.sha }}"
          git push
```

⚠️ Replace `YOUR_GITHUB_USERNAME` in both places.

Commit ✅

---

# 🧠 How It Works

### 🔔 Trigger

```yaml
on:
  push:
    paths:
      - 'website/**'
```

Only runs when files inside `website/` change.

---

### 🔄 Sync Logic

```bash
rsync -av --delete --exclude='.git' website/ repo-b/
```

✔ Copies everything  
✔ Deletes removed files  
✔ Preserves structure  
✔ Ignores `.git`

---

### 🛡 Safe Commit

```bash
git diff --cached --quiet || git commit
```

Prevents empty commits.

---

# 🧪 Step 6 — Testing

## ✅ Test Add

1. Add `website/test.md`
    
2. Commit
    
3. Go to **Actions tab**
    
4. Wait for green ✅
    
5. Check public repo
    

---

## ❌ Test Delete

1. Delete `test.md`
    
2. Commit
    
3. Watch Action run
    
4. File disappears from public repo
    

---

# 📁 Final Structure

```plaintext
obsidian-data/
├── .github/
│   └── workflows/
│       └── sync.yml
├── website/
│   ├── index.html
│   └── blogs/
│       └── post.md
└── private-notes/
    └── secrets.md
```

🛡 Only `website/` syncs  
🔒 Everything else stays private

---

# 🧯 Troubleshooting

### ❌ Workflow not triggering?

- Check file is in `website/`
    
- Ensure workflow is in `main` branch
    

---

### ❌ 403 Permission error?

- Regenerate PAT
    
- Ensure `repo` scope checked
    
- Re-add `SYNC_TOKEN`
    

---

### ❌ Files syncing incorrectly?

Make sure this line is exact:

```bash
rsync -av --delete --exclude='.git' website/ repo-b/
```

Trailing slashes matter.

---

# 🏁 Final Result

Every push inside:

```plaintext
obsidian-data/website/
```

Automatically mirrors to:

```plaintext
website-content/
```

✔ Additions  
✔ Modifications  
✔ Deletions  
✔ Fully automated

---
