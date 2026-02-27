To set up OpenClaw for a "dual setup" across Windows and Android, where data is stored in platform-specific Markdown files (window_data.md for Windows and android_data.md for Android), synced via GitHub (push/pull), and OpenClaw intelligently reads/writes based on the platform, we'll leverage OpenClaw's hook system. This allows custom event-driven logic without modifying core code. OpenClaw doesn't have built-in multi-device sync, but its hooks (e.g., for startup, bootstrap, or memory events) can run custom scripts to handle Git pull/merge/write/push operations.This assumes:

- Your GitHub repo contains both window_data.md and android_data.md (and other OpenClaw files like SOUL.md, MEMORY.md).
- OpenClaw is installed on both devices (Windows via npm; Android via Termux with Node.js/npm).
- Git is installed and configured on both (with GitHub auth, e.g., via PAT for push).
- The workspace is the Git repo root (configured via agents.defaults.workspace in config.json).
- Data refers to personalization/state (e.g., memory, sessions) typically in files like MEMORY.md. We'll treat window_data.md and android_data.md as platform-specific appendages that get merged into/read from a central file (e.g., MEMORY.md) for OpenClaw to "understand" the dual setup.

Step 1: Set Up Environment Variables for Platform DetectionOn each device, set a custom env var to identify the platform (since Node.js process.platform is 'win32' for Windows but 'linux' for Android/Termux—hard to distinguish without this).

- Windows (PowerShell): Add to system env vars (as before) or profile:
    
    ```text
    $env:OPENCLAW_PLATFORM = "windows"
    ```
    
- Android (Termux): Add to ~/.bashrc:
    
    ```text
    export OPENCLAW_PLATFORM="android"
    source ~/.bashrc
    ```
    

Step 2: Configure Workspace as Git Repo

1. On both devices: Set agents.defaults.workspace in config.json (e.g., C:\MyOpenClawFolder\config.json on Windows) to your repo root (e.g., "C:\\MyOpenClawFolder\\workspace"—use double backslashes).
2. Init Git if not already (in workspace root):
    
    ```text
    git init
    git remote add origin https://github.com/yourusername/your-repo.git
    ```
    
3. Create empty window_data.md and android_data.md in the workspace root, add/commit/push initially.

Step 3: Create a Custom Hook for Dual Setup LogicOpenClaw hooks are TypeScript/JS files that respond to events (e.g., gateway:startup for pull/merge on start, session-memory for write on data changes, agent:bootstrap for initial load). Place them in a hooks dir and enable in config.

1. Create Hooks Directory: In your workspace root, make hooks/my-dual-setup/ (or use ~/.openclaw/hooks/ if global).
2. Create package.json (for the hook):
    
    json
    
    ```json
    {
      "name": "dual-setup-hook",
      "version": "1.0.0",
      "description": "Handles dual Windows/Android data sync",
      "main": "handler.js"
    }
    ```
    
3. Create handler.js (the hook logic; use JS for simplicity, or TS if you have tsc setup):
    
    javascript
    
    ```javascript
    const fs = require('fs');
    const path = require('path');
    const { execSync } = require('child_process');
    
    const handler = async (event) => {
      const platform = process.env.OPENCLAW_PLATFORM;
      if (!platform) return; // Skip if not set
    
      const workspace = process.env.OPENCLAW_STATE_DIR || path.join(process.env.OPENCLAW_HOME, 'state'); // Adjust to your paths
      const centralFile = path.join(workspace, 'MEMORY.md'); // Or SOUL.md/whatever needs dual data
      const winFile = path.join(workspace, 'window_data.md');
      const androidFile = path.join(workspace, 'android_data.md');
      const myFile = platform === 'windows' ? winFile : androidFile;
      const otherFile = platform === 'windows' ? androidFile : winFile;
    
      try {
        if (event.type === 'gateway:startup' || event.type === 'agent:bootstrap') {
          // Pull from GitHub on startup/bootstrap
          execSync('git pull origin main', { cwd: workspace });
    
          // Merge other file into central for reading/understanding
          const otherData = fs.existsSync(otherFile) ? fs.readFileSync(otherFile, 'utf8') : '';
          const myData = fs.existsSync(myFile) ? fs.readFileSync(myFile, 'utf8') : '';
          const merged = `# Merged Data\n## From ${platform === 'windows' ? 'Android' : 'Windows'}\n${otherData}\n## Local\n${myData}`;
          fs.writeFileSync(centralFile, merged, 'utf8');
          console.log(`[dual-hook] Merged data into ${centralFile}`);
        }
    
        if (event.type === 'session-memory' || event.type === 'command:new') { // On write events
          // Append new data to my platform file (replace with actual data logic; e.g., event.data)
          const newData = event.data || 'New entry: ' + new Date().toISOString() + '\n'; // Placeholder; extract from event
          fs.appendFileSync(myFile, newData, 'utf8');
          console.log(`[dual-hook] Wrote to ${myFile}`);
    
          // Commit and push to GitHub
          execSync(`git add ${myFile}`, { cwd: workspace });
          execSync('git commit -m "Updated data from ' + platform + '"', { cwd: workspace });
          execSync('git push origin main', { cwd: workspace });
        }
      } catch (error) {
        console.error(`[dual-hook] Error: ${error.message}`);
      }
    };
    
    module.exports = handler; // For older Node; or export default handler;
    ```
    
    - How it works:
        - On startup/bootstrap: Pulls latest, merges the other platform's file into central (e.g., MEMORY.md) for OpenClaw to read/understand both.
        - On memory/command events: Appends new data to your platform's file, commits/pushes.
        - Customize newData based on event (e.g., event.session.memory if available).
        - Error handling is basic; add more.
4. Enable the Hook:
    - Run openclaw hooks install ./hooks/my-dual-setup (from workspace).
    - Or add to config.json under hooks.enabled: ["dual-setup-hook"].
    - Run openclaw hooks enable dual-setup-hook.
    - Test: openclaw hooks list to verify.

Step 4: Usage on Each Device

1. On Startup: Run openclaw gateway—hook pulls/merges automatically.
2. When Data Changes: E.g., new session/memory—hook writes to your file, pushes.
3. Manual Sync: If needed, run git pull before commands.
4. On Android (Termux): Ensure Git/Node/OpenClaw installed (pkg install git nodejs, npm i -g openclaw).

Notes

- Security: Don't push sensitive data (add to .gitignore if needed).
- Conflicts: Git may conflict on push/pull; resolve manually or improve merge logic (e.g., use sections in MD).
- Events: Adjust based on your needs (e.g., add command for all commands).
- Testing: Start with logs/console; run openclaw doctor for issues.
- If hooks don't fire, check config/docs for your version.

This makes OpenClaw "understand" the dual setup via hooks and Git.

docs.openclaw.ai +5