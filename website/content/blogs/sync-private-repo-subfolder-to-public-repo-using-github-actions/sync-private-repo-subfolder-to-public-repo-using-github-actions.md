# Sync a Private Repo Subfolder to a Public Repo Using GitHub Actions

This guide walks you through everything from scratch — creating both repos, setting up permissions, and automating the sync so any change (including deletions) in your private repo's subfolder automatically reflects in your public repo.

---

## What We Are Building

```
Private Repo (obsidian-data)
└── website/                ← only this folder gets synced
    ├── index.html
    ├── style.css
    └── blogs/
        └── post.md

Public Repo (website-content)
├── index.html              ← mirrors website/ folder exactly
├── style.css
└── blogs/
    └── post.md
```

Whenever you push a change to the `website/` folder in your private repo, GitHub Actions will automatically copy those changes to your public repo — including deletions.

---

## Step 1: Create the Private Repo (Repo A)

1. Go to [github.com](https://github.com/) and log in
2. Click the **+** icon at the top right → **New repository**
3. Fill in:
    - **Repository name:** `obsidian-data`
    - **Visibility:** Select **Private**
    - Check **Add a README file** (so the repo is not empty)
4. Click **Create repository**

Now create the `website` folder inside it:

1. Click **Add file** → **Create new file**
2. In the filename box type: `website/readme.md`
    - Typing the `/` automatically creates the folder
3. Add any content like `# My Website`
4. Click **Commit new file**

---

## Step 2: Create the Public Repo (Repo B)

1. Click **+** → **New repository**
2. Fill in:
    - **Repository name:** `website-content`
    - **Visibility:** Select **Public**
    - Check **Add a README file**
3. Click **Create repository**

> ⚠️ The public repo must have at least one commit (not be completely empty) otherwise the sync will fail. The README file handles this.

---

## Step 3: Create a Personal Access Token (PAT)

The GitHub Action in your private repo needs permission to push to your public repo. You give it this permission through a Personal Access Token.

1. Click your **profile picture** (top right)
2. Click **Settings**
3. Scroll all the way down on the left sidebar → click **Developer settings**
4. Click **Personal access tokens** → **Tokens (classic)**
5. Click **Generate new token** → **Generate new token (classic)**
6. Fill in:
    - **Note:** `sync-obsidian-to-website` (a label so you remember what it is)
    - **Expiration:** Choose `No expiration` or set a date
    - **Scopes:** Check the box next to `repo` (the top-level one — this gives full repo read/write access)
7. Scroll down → click **Generate token**
8. **COPY THE TOKEN NOW** — it starts with `ghp_...` and you will never see it again after leaving this page. Save it in Notepad temporarily.

---

## Step 4: Add the Token as a Secret in Repo A

You store the token as a secret so GitHub Actions can use it securely without exposing it in your code.

1. Go to your **private repo** (`obsidian-data`)
2. Click the **Settings** tab (inside the repo, not your account settings)
3. On the left sidebar click **Secrets and variables** → **Actions**
4. Click **New repository secret**
5. Fill in:
    - **Name:** `SYNC_TOKEN`
    - **Secret:** Paste the token you copied in Step 3
6. Click **Add secret**

You should now see `SYNC_TOKEN` listed. The value is hidden and encrypted — nobody can read it, but GitHub Actions can use it.

---

## Step 5: Create the GitHub Actions Workflow

This is the automation file that does the actual syncing.

1. Go to your **private repo** (`obsidian-data`)
2. Click **Add file** → **Create new file**
3. In the filename box type exactly: `.github/workflows/sync.yml`
    - GitHub will create the `.github` and `workflows` folders automatically as you type the slashes
4. Paste the following content:

```yaml
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

5. **Replace `YOUR_GITHUB_USERNAME`** in two places with your actual GitHub username. For example if your username is `john`:
    
    - `echo "https://vineel:${TOKEN}@github.com"`
    - `git clone https://github.com/john/website-content.git repo-b`
6. Scroll down → click **Commit new file**
    

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

## Step 7: Clone the Private Repo to Your Computer

Until now everything was done on the GitHub website. In real life you work on your files locally on your computer and then push to GitHub — that push is what triggers the sync.

### Install Git (if you don't have it)

- **Windows:** Download from [git-scm.com](https://git-scm.com/) and install
- **Mac:** Open Terminal and type `git --version` — it will prompt you to install if missing
- **Linux:** Run `sudo apt install git`

### Clone the repo

1. Go to your **private repo** (`obsidian-data`) on GitHub
2. Click the green **Code** button
3. Copy the HTTPS URL — it looks like:
    
    ```
    https://github.com/YOUR_USERNAME/obsidian-data.git
    ```
    
4. Open your Terminal (Mac/Linux) or Git Bash (Windows)
5. Navigate to the folder where you want to keep the project:
    
    ```bash
    cd Documents
    ```
    
6. Clone the repo:
    
    ```bash
    git clone https://github.com/YOUR_USERNAME/obsidian-data.git
    ```
    
7. Enter the folder:
    
    ```bash
    cd obsidian-data
    ```
    

Your private repo is now on your computer. The folder structure looks like:

```
obsidian-data/
├── .github/
│   └── workflows/
│       └── sync.yml
├── website/
│   └── readme.md
└── README.md
```

---

## Step 8: Make Changes Locally and Push to Trigger the Sync

This is the normal day-to-day workflow. You edit files on your computer, push to GitHub, and the sync to the public repo happens automatically.

### Example — adding a new file

1. Open the `obsidian-data` folder on your computer
2. Go into the `website/` folder and create a new file, for example `index.html`
3. Add some content and save it
4. Go back to your terminal and run these commands one by one:

```bash
# See what files changed
git status

# Stage all changes
git add .

# Commit with a message
git commit -m "Add index.html"

# Push to GitHub
git push
```

5. The moment `git push` completes, go to your private repo on GitHub → **Actions** tab
6. You will see the workflow **"Sync website to public repo"** is now running
7. Once it finishes (green ✅), open your **public repo** (`website-content`) — `index.html` will be there

### Example — deleting a file

1. Delete any file inside the `website/` folder on your computer
2. In terminal:

```bash
git add .
git commit -m "Remove old file"
git push
```

3. The workflow triggers → the same file gets deleted from the public repo automatically

### Example — editing an existing file

1. Open any file inside `website/` and make changes
2. Save the file
3. In terminal:

```bash
git add .
git commit -m "Update content"
git push
```

4. The workflow triggers → the updated file appears in the public repo

> 💡 **Key rule:** The workflow only triggers when your push contains changes inside the `website/` folder. If you only edit files outside `website/` (like private notes), the public repo is not touched at all.

---

## Step 9: Test That It Works

### Test adding a file

1. Go to your **private repo** (`obsidian-data`)
2. Navigate into the `website/` folder
3. Click **Add file** → **Create new file**
4. Name it `test.md` and add some content
5. Click **Commit new file**
6. Go to the **Actions** tab of your private repo
7. You should see a workflow run called **"Sync website to public repo"** running
8. Click on it — you can watch the live logs
9. Once it shows a green ✅, go to your **public repo** (`website-content`) and verify `test.md` is there

### Test deleting a file

1. In your private repo, go into `website/` and open `test.md`
2. Click the **trash icon** (Delete file)
3. Commit the deletion
4. Watch the Actions tab — the workflow runs
5. Go to your public repo — `test.md` should be gone

---

## Step 10: Your Folder Structure

After setup your private repo will look like this:

```
obsidian-data/                    ← private repo root
├── .github/
│   └── workflows/
│       └── sync.yml              ← the automation file
├── website/                      ← THIS folder syncs to public repo
│   ├── index.html
│   └── blogs/
│       └── post.md
└── other-private-stuff/          ← this does NOT sync anywhere
    └── notes.md
```

Only the `website/` folder gets synced. Everything else in `obsidian-data` stays private.

---

## Troubleshooting

**Workflow doesn't appear in Actions tab?**

- Make sure you committed the `sync.yml` file to the default branch (usually `main`)
- Make sure the change you pushed was inside the `website/` folder

**403 Permission denied error on push?**

- Your PAT token may not have the `repo` scope — regenerate it with full `repo` scope checked
- Make sure `YOUR_GITHUB_USERNAME` in the workflow file matches your actual GitHub username exactly (case-sensitive)
- Delete the old `SYNC_TOKEN` secret and add it again with the new token

**Files going into wrong folder in public repo?**

- Make sure the rsync line is `website/ repo-b/` — the trailing slashes matter
- Make sure all git commands are inside `cd repo-b` step

**Workflow runs but nothing changes in public repo?**

- Check the Actions logs — look for any error in red
- Make sure your public repo (`website-content`) is not empty — it needs at least one commit

**Workflow triggered but says "nothing to commit"?**

- This is fine — it means the public repo already has the latest files. Make an actual change and try again.

---

## Summary

|Step|What You Did|
|---|---|
|1|Created private repo `obsidian-data` with a `website/` folder|
|2|Created public repo `website-content`|
|3|Generated a Personal Access Token with `repo` scope|
|4|Added the token as a secret called `SYNC_TOKEN` in private repo|
|5|Created `.github/workflows/sync.yml` with the sync automation|
|6|Understood what each part of the workflow does|
|7|Cloned the private repo to your local computer|
|8|Made changes locally and pushed to trigger the sync|
|9|Tested adding and deleting files|

From now on, every time you push changes to the `website/` folder in `obsidian-data`, GitHub Actions will automatically mirror those changes — including deletions — to `website-content`.