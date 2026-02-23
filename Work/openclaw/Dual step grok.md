Revised Dual-System Setup for OpenClaw with GitHub Sync (Windows and Android)I've updated the setup based on your clarifications: State dir (state/) and config (config.json) now go under the local folder (C:\openclaw\local\), keeping them platform-specific and not shared.

Shared data folder (C:\openclaw\shareddata\) contains only windows.md and android.md (Git-initialized and pushed to GitHub for sync).

Hooks are placed in the local folder (C:\openclaw\local\hooks\), and we'll create platform-specific hook files if needed (e.g., separate JS for Windows vs. Android logic, but shared code with platform detection).

No integration of startup/start OpenClaw into hooks—hooks focus only on write (during responses), and we'll handle pull/push as separate manual scripts (not hooks, since hooks are event-based; these can be run as "buttons" via batch/shell).

"Understanding" (reading/merging the other file) happens during the pull script (manual trigger), merging into a local central file (e.g., MEMORY.md in state dir) for OpenClaw to use.

Writes: For every response (via command or session-memory hook events), automatically append to the respective platform file in shared (e.g., Windows writes only to windows.md).

Pull: Manual script to pull from GitHub and merge/understand the other file into local MEMORY.md.

Push: Manual script to commit/push changes to GitHub (only your platform's file).



This keeps shared minimal and Git-syncable, while local handles everything else.Step 1: Folder Structure and Installation (Same on Both Platforms, with Adjustments)Create Folders:Windows: C:\openclaw\local\ (for install, state, config, hooks) and C:\openclaw\shareddata\ (only for windows.md and android.md).

Android (Termux): /storage/emulated/0/openclaw/local/ and /storage/emulated/0/openclaw/shareddata/.


Install OpenClaw in Local Folder:Update env vars to point state/config to local (not shared).

Windows (PowerShell as admin):


$env:OPENCLAW_HOME = "C:\openclaw\local"
$env:OPENCLAW_STATE_DIR = "C:\openclaw\local\state"  # Now in local
$env:OPENCLAW_CONFIG_PATH = "C:\openclaw\local\config.json"  # Now in local
# Make persistent
iwr -useb https://openclaw.ai/install.ps1 | iex


Android (Termux):


pkg install nodejs
npm install -g openclaw
export OPENCLAW_HOME="/storage/emulated/0/openclaw/local"
export OPENCLAW_STATE_DIR="/storage/emulated/0/openclaw/local/state"
export OPENCLAW_CONFIG_PATH="/storage/emulated/0/openclaw/local/config.json"
# Add to ~/.bashrc
openclaw onboard


In config.json (now in local), set agents.defaults.workspace to your local workspace path (e.g., "C:\\openclaw\\local\\workspace"). This keeps workspace local too.


Platform Detection Env Var (as before):Windows: $env:OPENCLAW_PLATFORM = "windows"

Android: export OPENCLAW_PLATFORM="android"


Create Platform Files in Shared:In C:\openclaw\shareddata\, create empty windows.md and android.md.


Step 2: Initialize Git Only in Shared Data Folder (as before)Windows:


cd C:\openclaw\shareddata
git init
git add .
git commit -m "Initial platform files"
git remote add origin https://github.com/yourusername/openclaw-shared.git
git branch -M main
git push -u origin main


Android: Clone as before (git clone ... . into shareddata).



Step 3: Implement Hooks for Writes (in Local Folder)Hooks go in C:\openclaw\local\hooks\dual-write\ (or Android equiv). Since logic is similar but platform-specific, we'll use one shared handler.js with env var detection (copy to both devices). This hook only handles writes during responses (e.g., on command or session-memory events)—no pull/push/understand here.Create C:\openclaw\local\hooks\dual-write\package.json:json


{
  "name": "dual-write-hook",
  "version": "1.0.0",
  "main": "handler.js"
}


Create C:\openclaw\local\hooks\dual-write\handler.js (same on both platforms):javascript


const fs = require('fs');
const path = require('path');

module.exports = async (event) => {
  const platform = process.env.OPENCLAW_PLATFORM;
  if (!platform) return;

  const sharedDir = 'C:\\openclaw\\shareddata'; // Adjust for Android: '/storage/emulated/0/openclaw/shareddata'
  const winFile = path.join(sharedDir, 'windows.md');
  const androidFile = path.join(sharedDir, 'android.md');
  const myFile = platform === 'windows' ? winFile : androidFile;

  try {
    if (event.type === 'command' || event.type === 'session-memory') { // On responses/events
      // Append new data to my file only (placeholder; customize with event.data)
      const newData = event.data || `New ${platform} response entry: ${new Date().toISOString()}\n`;
      fs.appendFileSync(myFile, newData, 'utf8');
      console.log('[dual-write] Appended to my file');
    }
  } catch (error) {
    console.error('[dual-write] Error:', error.message);
  }
};



Note: On Android, edit the sharedDir path in the file (or make it env-var based, e.g., process.env.OPENCLAW_SHARED_DIR). This makes it "different" per system if you copy/edit separately.

This ensures writes only to own file, never the other.


Install and Enable:openclaw hooks install ./hooks/dual-write

Add to local config.json under hooks.enabled: ["dual-write-hook"]

openclaw hooks enable dual-write-hook


Step 4: Manual Scripts for Pull (with Understand) and Push (as "Buttons")These are separate batch/shell scripts in local (e.g., C:\openclaw\local\scripts\)—run them manually as triggers (e.g., desktop shortcuts on Windows, Termux tasks on Android). No integration with startup.Create Scripts Folder: C:\openclaw\local\scripts\

Pull Script (pull-and-understand.bat on Windows):


@echo off
set SHARED_DIR=C:\openclaw\shareddata
set PLATFORM=windows
set CENTRAL_FILE=C:\openclaw\local\state\MEMORY.md
set OTHER_FILE=%SHARED_DIR%\android.md  :: For Windows; swap for Android

cd %SHARED_DIR%
git pull origin main

:: Merge/understand other file into local central
echo # From Other Platform (Android) >> %CENTRAL_FILE%
type %OTHER_FILE% >> %CENTRAL_FILE%
echo [pull-understand] Merged other data into central
pause



On Android (pull-and-understand.sh in Termux):


#!/bin/bash
SHARED_DIR="/storage/emulated/0/openclaw/shareddata"
PLATFORM="android"
CENTRAL_FILE="/storage/emulated/0/openclaw/local/state/MEMORY.md"
OTHER_FILE="$SHARED_DIR/windows.md"

cd $SHARED_DIR
git pull origin main

echo "# From Other Platform (Windows)" >> $CENTRAL_FILE
cat $OTHER_FILE >> $CENTRAL_FILE
echo "[pull-understand] Merged other data into central"



Make executable: chmod +x pull-and-understand.sh


How it works: Pulls, then appends the other file to local MEMORY.md for OpenClaw to "understand" (read during sessions).


Push Script (push.bat on Windows):


@echo off
set SHARED_DIR=C:\openclaw\shareddata
set PLATFORM=windows
set MY_FILE=%SHARED_DIR%\windows.md  :: For Windows; swap for Android

cd %SHARED_DIR%
git add %MY_FILE%
git commit -m "Push from %PLATFORM%"
git push origin main
echo [push] Changes pushed to GitHub
pause



On Android (push.sh):


#!/bin/bash
SHARED_DIR="/storage/emulated/0/openclaw/shareddata"
PLATFORM="android"
MY_FILE="$SHARED_DIR/android.md"

cd $SHARED_DIR
git add $MY_FILE
git commit -m "Push from $PLATFORM"
git push origin main
echo "[push] Changes pushed to GitHub"



chmod +x push.sh


Step 5: UsageStart OpenClaw normally: openclaw gateway (no auto-pull).

To pull and understand: Run pull-and-understand.bat/sh (e.g., before sessions).

Interact: Run commands/chats—hook auto-writes to your .md in shared.

To push: Run push.bat/sh (manual trigger).

On the other device: Pull to get updates, understand merges to local MEMORY.md.



This separates concerns fully. Customize newData in hook or paths as needed. Test with dummy entries.



Android (Termux): /storage/emulated/0/openclaw/local/ and /storage/emulated/0/openclaw/shareddata/.


Install OpenClaw in Local Folder:Update env vars to point state/config to local (not shared).

Windows (PowerShell as admin):


$env:OPENCLAW_HOME = "C:\openclaw\local"
$env:OPENCLAW_STATE_DIR = "C:\openclaw\local\state"  # Now in local
$env:OPENCLAW_CONFIG_PATH = "C:\openclaw\local\config.json"  # Now in local
# Make persistent
iwr -useb https://openclaw.ai/install.ps1 | iex


Android (Termux):


pkg install nodejs
npm install -g openclaw
export OPENCLAW_HOME="/storage/emulated/0/openclaw/local"
export OPENCLAW_STATE_DIR="/storage/emulated/0/openclaw/local/state"
export OPENCLAW_CONFIG_PATH="/storage/emulated/0/openclaw/local/config.json"
# Add to ~/.bashrc
openclaw onboard


In config.json (now in local), set agents.defaults.workspace to your local workspace path (e.g., "C:\\openclaw\\local\\workspace"). This keeps workspace local too.


Platform Detection Env Var (as before):Windows: $env:OPENCLAW_PLATFORM = "windows"

Android: export OPENCLAW_PLATFORM="android"


Create Platform Files in Shared:In C:\openclaw\shareddata\, create empty windows.md and android.md.


Step 2: Initialize Git Only in Shared Data Folder (as before)Windows:


cd C:\openclaw\shareddata
git init
git add .
git commit -m "Initial platform files"
git remote add origin https://github.com/yourusername/openclaw-shared.git
git branch -M main
git push -u origin main


Android: Clone as before (git clone ... . into shareddata).



Step 3: Implement Hooks for Writes (in Local Folder)Hooks go in C:\openclaw\local\hooks\dual-write\ (or Android equiv). Since logic is similar but platform-specific, we'll use one shared handler.js with env var detection (copy to both devices). This hook only handles writes during responses (e.g., on command or session-memory events)—no pull/push/understand here.Create C:\openclaw\local\hooks\dual-write\package.json:json


{
  "name": "dual-write-hook",
  "version": "1.0.0",
  "main": "handler.js"
}


Create C:\openclaw\local\hooks\dual-write\handler.js (same on both platforms):javascript


const fs = require('fs');
const path = require('path');

module.exports = async (event) => {
  const platform = process.env.OPENCLAW_PLATFORM;
  if (!platform) return;

  const sharedDir = 'C:\\openclaw\\shareddata'; // Adjust for Android: '/storage/emulated/0/openclaw/shareddata'
  const winFile = path.join(sharedDir, 'windows.md');
  const androidFile = path.join(sharedDir, 'android.md');
  const myFile = platform === 'windows' ? winFile : androidFile;

  try {
    if (event.type === 'command' || event.type === 'session-memory') { // On responses/events
      // Append new data to my file only (placeholder; customize with event.data)
      const newData = event.data || `New ${platform} response entry: ${new Date().toISOString()}\n`;
      fs.appendFileSync(myFile, newData, 'utf8');
      console.log('[dual-write] Appended to my file');
    }
  } catch (error) {
    console.error('[dual-write] Error:', error.message);
  }
};



Note: On Android, edit the sharedDir path in the file (or make it env-var based, e.g., process.env.OPENCLAW_SHARED_DIR). This makes it "different" per system if you copy/edit separately.

This ensures writes only to own file, never the other.


Install and Enable:openclaw hooks install ./hooks/dual-write

Add to local config.json under hooks.enabled: ["dual-write-hook"]

openclaw hooks enable dual-write-hook


Step 4: Manual Scripts for Pull (with Understand) and Push (as "Buttons")These are separate batch/shell scripts in local (e.g., C:\openclaw\local\scripts\)—run them manually as triggers (e.g., desktop shortcuts on Windows, Termux tasks on Android). No integration with startup.Create Scripts Folder: C:\openclaw\local\scripts\

Pull Script (pull-and-understand.bat on Windows):


@echo off
set SHARED_DIR=C:\openclaw\shareddata
set PLATFORM=windows
set CENTRAL_FILE=C:\openclaw\local\state\MEMORY.md
set OTHER_FILE=%SHARED_DIR%\android.md  :: For Windows; swap for Android

cd %SHARED_DIR%
git pull origin main

:: Merge/understand other file into local central
echo # From Other Platform (Android) >> %CENTRAL_FILE%
type %OTHER_FILE% >> %CENTRAL_FILE%
echo [pull-understand] Merged other data into central
pause



On Android (pull-and-understand.sh in Termux):


#!/bin/bash
SHARED_DIR="/storage/emulated/0/openclaw/shareddata"
PLATFORM="android"
CENTRAL_FILE="/storage/emulated/0/openclaw/local/state/MEMORY.md"
OTHER_FILE="$SHARED_DIR/windows.md"

cd $SHARED_DIR
git pull origin main

echo "# From Other Platform (Windows)" >> $CENTRAL_FILE
cat $OTHER_FILE >> $CENTRAL_FILE
echo "[pull-understand] Merged other data into central"



Make executable: chmod +x pull-and-understand.sh


How it works: Pulls, then appends the other file to local MEMORY.md for OpenClaw to "understand" (read during sessions).


Push Script (push.bat on Windows):


@echo off
set SHARED_DIR=C:\openclaw\shareddata
set PLATFORM=windows
set MY_FILE=%SHARED_DIR%\windows.md  :: For Windows; swap for Android

cd %SHARED_DIR%
git add %MY_FILE%
git commit -m "Push from %PLATFORM%"
git push origin main
echo [push] Changes pushed to GitHub
pause



On Android (push.sh):


#!/bin/bash
SHARED_DIR="/storage/emulated/0/openclaw/shareddata"
PLATFORM="android"
MY_FILE="$SHARED_DIR/android.md"

cd $SHARED_DIR
git add $MY_FILE
git commit -m "Push from $PLATFORM"
git push origin main
echo "[push] Changes pushed to GitHub"



chmod +x push.sh


Step 5: UsageStart OpenClaw normally: openclaw gateway (no auto-pull).

To pull and understand: Run pull-and-understand.bat/sh (e.g., before sessions).

Interact: Run commands/chats—hook auto-writes to your .md in shared.

To push: Run push.bat/sh (manual trigger).

On the other device: Pull to get updates, understand merges to local MEMORY.md.



This separates concerns fully. Customize newData in hook or paths as needed. Test with dummy entries.


