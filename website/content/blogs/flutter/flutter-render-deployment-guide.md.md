# Flutter Web → Render Deployment Guide

---

## Previous Setup (What We Were Doing Before)

The old approach involved:

1. Building Flutter web **locally** with `flutter build web`
2. Manually copying the `build/web` output to a **second GitHub repo**
3. Publishing that second repo to Render as a static site

**Problems with this approach:**

- Every change required building locally and pushing to two repos
- Error-prone and repetitive manual process
- No automation — easy to forget to push the build
- Messy workflow — source code and built files in separate repos

---

## New Setup (What We Do Now)

One repo. Push code → merge to production branch → Render builds and deploys automatically.

---

## Complete Setup Steps

### Step 1 — Create GitHub Repo from Android Studio

1. Open your Flutter project in Android Studio
2. Go to **VCS** → **Enable Version Control Integration** → select **Git** → OK
3. Go to **VCS** → **Share Project on GitHub**
4. Login to GitHub if prompted
5. Give your repo a name, choose public or private
6. Click **Share** — Android Studio creates the repo and pushes everything

---
### Step 2 — Create `build.sh` in Project Root

In Android Studio left panel → right click project root → **New** → **File** → name it `build.sh`

Paste this content:

bash

```bash
#!/bin/bash
set -e

if [ ! -d "$HOME/flutter" ]; then
  git clone https://github.com/flutter/flutter.git -b stable --depth 1 ~/flutter
fi

export PATH="$PATH:~/flutter/bin"

flutter pub get
flutter build web
```

**Why the `if` check:** Render caches files between builds. Without this check, the second deploy would fail with `fatal: destination path already exists` because Flutter was already downloaded in the previous build. The `if` check skips cloning Flutter if it already exists, saving time. Your app code is always freshly pulled from GitHub by Render regardless.

Then commit and push it:

**VCS** → **Commit** → check `build.sh` → write commit message → **Commit and Push**

---

### Step 3 — Create `production` Branch

Open the terminal in Android Studio: **View** → **Tool Windows** → **Terminal**

Run:

```bash
# Create production branch and push it
git checkout -b production
git push origin production

# Switch back to master for daily work
git checkout master
```

You now have two branches:

- `master` → your daily workspace, pushing here does NOT trigger Render
- `production` → deploy trigger, pushing here tells Render to build and deploy

---

### Step 4 — Create Static Site on Render

1. Go to [render.com](https://render.com/) → **New** → **Static Site**
2. Connect your GitHub repo
3. Fill in the settings:

|Field|Value|
|---|---|
|**Branch**|`production`|
|**Build Command**|`bash build.sh`|
|**Publish Directory**|`build/web`|

**Why these values:**

- **Branch: production** — Render only watches this branch, ignores `master`
- **Build Command: bash build.sh** — Render runs your script which installs Flutter and builds the app
- **Publish Directory: build/web** — after building, Flutter outputs files here, Render serves `index.html` from this folder

---

### Step 5 — Add Rewrite Rule

In Render → your site → **Settings** → **Redirect and Rewrite Rules** → **Add Rule**:

|Source|Destination|Action|
|---|---|---|
|`/*`|`/index.html`|Rewrite|

**Why this is needed:**

Flutter web is a Single Page Application (SPA). Only one real file exists — `index.html`. Flutter handles all routing inside the browser. Without this rule, visiting any URL other than `/` would return a 404 because those files don't physically exist. This rule tells Render: "for any URL, just serve `index.html` and let Flutter handle the rest."

---

## Daily Workflow

### Normal Development (no deploy)

```bash
# You are on master
# Make your changes in Flutter...

git add .
git commit -m "your message"
git push origin master
# Nothing happens on Render ✓
```

### When Ready to Deploy

```bash
git checkout production
git merge master
git push origin production
# Render detects this push → runs build.sh → deploys automatically ✓

# Switch back to master to continue working
git checkout master
```

---

## How It All Works Together

```
You write code on master
        ↓
Push to master (Render ignores this)
        ↓
When ready → merge master into production
        ↓
Push production to GitHub
        ↓
Render detects push to production branch
        ↓
Render runs build.sh:
  → installs Flutter
  → runs flutter pub get
  → runs flutter build web
        ↓
Render serves build/web to users
        ↓
Your site is live and updated ✓
```

---

## Quick Reference Commands

```bash
# Daily work
git add .
git commit -m "message"
git push origin master

# Deploy
git checkout production
git merge master
git push origin production
git checkout master
```