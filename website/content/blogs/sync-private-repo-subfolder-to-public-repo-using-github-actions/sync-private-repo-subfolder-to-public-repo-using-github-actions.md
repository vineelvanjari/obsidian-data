# 🔄 Sync a Private Repo Subfolder to a Public Repo Using GitHub Actions 🚀

Building in private while publishing in public is a very common developer workflow 🔐🌍. You might store your personal notes, experiments, drafts, and internal files inside a private repository — but only a specific folder needs to be exposed to the world. Instead of manually copying files every time you make a change, we can design an automated system that mirrors one subfolder from a private repository into a public repository using GitHub Actions ⚙️🤖.

This guide walks you through the complete setup from scratch, explains why each step matters, and ensures that even file deletions are perfectly synced. By the end, your workflow will be fully automatic — you push once, and everything updates on its own ✨.

---

## 🎯 What We Are Building

At a high level, we are separating concerns: one repository is your secure workspace, and the other is your public-facing content.

```
Private Repo (obsidian-data) 🔒
└── website/ 🌍
    ├── index.html
    ├── style.css
    └── blogs/
        └── post.md

Public Repo (website-content) 🌐
├── index.html
├── style.css
└── blogs/
    └── post.md
```

Only the `website/` folder from the private repository is mirrored. Everything else — such as personal notes, drafts, or sensitive files — remains completely private and untouched 🔐.

Whenever you push a change inside the `website/` folder, GitHub Actions automatically:

• Detects the change  
• Copies updated files  
• Removes deleted files  
• Commits the changes  
• Pushes to the public repository

The result is a perfect mirror between `website/` and your public repo — fully automated and reliable ⚡.

---

# 🛠 Step 1: Create the Private Repository

Start by creating your private repository. This will act as your master workspace. It contains everything — including things that should never be public.

When creating the repository:

• Name it `obsidian-data`  
• Set visibility to **Private**  
• Add a README so it isn’t empty

After creation, create a folder named `website/`. This folder is special — it is the only folder that will sync to the public repository. Think of it as your "export zone" 🌍.

Inside this folder, you can structure your website however you like — HTML files, Markdown blogs, CSS, images, anything.

---

# 🌍 Step 2: Create the Public Repository

Now create a second repository named `website-content`. This one must be **Public**.

It will act purely as the mirrored output. You won’t manually edit this repository. It is controlled entirely by automation 🤖.

Important: The public repository must have at least one commit. Adding a README during creation solves this automatically.

---

# 🔑 Step 3: Generate a Personal Access Token (PAT)

GitHub Actions cannot push to another repository unless you explicitly grant permission. This permission is provided using a Personal Access Token (PAT) 🔐.

When generating the token:

• Use a meaningful name like `sync-obsidian-to-website`  
• Choose expiration wisely  
• Enable the full `repo` scope

The `repo` scope is critical because it allows read and write access. Without it, the automation will fail with permission errors.

After generating the token, copy it immediately. You will not be able to see it again.

---

# 🔐 Step 4: Store the Token Securely

Never hardcode your token inside the workflow file. That would expose it publicly. Instead, GitHub provides encrypted repository secrets.

Inside your private repository settings:

• Go to Secrets and Variables → Actions  
• Create a new secret named `SYNC_TOKEN`  
• Paste your token

This keeps the token encrypted and safe. The workflow can access it, but humans cannot read it 🔒.

---

# ⚙️ Step 5: Create the Automation Workflow

Now we create the automation engine. This is done using a workflow file placed at:

```
.github/workflows/sync.yml
```

This file defines:

• When the workflow should run  
• What environment it should use  
• What steps it should execute

The trigger section ensures the workflow runs only when files inside `website/` change. This prevents unnecessary runs when unrelated files are modified.

The job then:

1. Checks out the private repository
    
2. Clones the public repository into a temporary folder
    
3. Uses `rsync` to copy files while removing deleted ones
    
4. Commits only if there are actual changes
    
5. Pushes the updates
    

The `rsync --delete` flag is extremely important. Without it, deleted files in your private repo would continue to exist in the public repo, causing mismatches.

Trailing slashes in the rsync command also matter. They control whether the folder itself is copied or only its contents.

---

# 🧪 Testing the System

After setup, testing is simple.

First test adding a file. Create something like `website/test.md` and commit it. Open the Actions tab and watch the workflow execute. After it completes successfully, check your public repository — the file should appear.

Next test deletion. Remove `test.md` from the private repository and commit again. Once the workflow finishes, the file should disappear from the public repository as well. This confirms the `--delete` behavior is working correctly.

---

# 🗂 Final Structure Overview

Your private repository will look like this:

```
obsidian-data/
├── .github/
│   └── workflows/
│       └── sync.yml
├── website/
│   ├── index.html
│   └── blogs/
│       └── post.md
└── other-private-stuff/
    └── notes.md
```

Only `website/` syncs. Everything else remains secure and internal.

---

# 🏁 Final Outcome

You now have a clean separation between private development and public publishing 🔐🌐.

Every time you push changes inside the `website/` folder:

• Automation runs  
• Files update  
• Deletions sync  
• Public repo mirrors perfectly

No manual copying. No risk of forgetting files. No inconsistencies.

You focus on building and writing ✍️.

GitHub handles deployment-style syncing automatically 🤖✨.