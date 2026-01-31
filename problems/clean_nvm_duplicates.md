# Remove nvm and clean duplicate npm/node

## Date
2026-01-31

## Environment / Context
- OS / Platform: macOS (Apple Silicon, Homebrew at /opt/homebrew)
- Node: v25.2.1 (Homebrew-managed)
- npm: v11.6.2 (Homebrew-managed)
- nvm: residual ~/.nvm (only alias directory present)
- Tools used: Homebrew, nvm, shell (zsh), git

## Problem Description
I had a mix of Node/npm/nvm artifacts on my Mac. Node and npm were correctly installed via Homebrew, but leftover nvm files and potential old npm/node artifacts could cause confusion or conflicts. I wanted a safe, repeatable way to remove nvm and its leftovers, back up any changed shell rc files, and ensure a clean Homebrew-managed Node/npm environment.

## Root Cause
- Historical installs and switches between tools (Homebrew, nvm, manual installs) left leftover files and shell rc lines referring to nvm.
- Even when Homebrew manages Node/npm correctly, leftover nvm configuration can cause ambiguity, especially if nvm is partially present or referenced in rc files.
- Without cleaning, shell startup may try to source non-existent nvm files or create inconsistent Node versions between terminal sessions.

## Solution
1. Verify current executables and versions
   - Commands:
     - which -a node npm npx
     - node -v
     - npm -v
   - Confirmed binaries came from /opt/homebrew and versions shown above.

2. Back up shell rc files before making changes
   - Files to back up: ~/.zshrc, ~/.bashrc, ~/.profile, ~/.bash_profile, ~/.config/fish/config.fish (if present).
   - Always keep a timestamped backup directory.

3. Remove nvm safely (scripted approach)
   - Create a script (remove-nvm.sh) that:
     - Backs up rc files
     - Moves ~/.nvm and common install paths to backup (instead of immediate delete)
     - Removes lines that load nvm (patterns referencing nvm.sh, NVM_DIR, nvm bash_completion) from rc files
     - Optionally runs brew uninstall nvm if installed via Homebrew
     - Unsets nvm in current session where possible
   - Make script executable and run it after review:
     - chmod +x remove-nvm.sh
     - ./remove-nvm.sh

   (I used and recommend the script that backs up and moves directories rather than force-deleting.)

4. Clear npm caches and user-global dirs (optional)
   - Commands:
     - npm cache clean --force
     - rm -rf ~/.npm ~/.npm-global    # only if you know you don't need them

5. Configure a safe user-global npm prefix to avoid sudo (recommended)
   - Commands:
     - mkdir -p "$HOME/.npm-global"
     - npm config set prefix "$HOME/.npm-global"
     - Add to shell rc: export PATH="$HOME/.npm-global/bin:$PATH"
     - source ~/.zshrc

6. Final verification
   - which -a node npm npx
   - node -v
   - npm -v
   - npm ls -g --depth=0  (check globals)

7. Restore if needed
   - If anything breaks, restore backups from the timestamped backup directory created by the script.

## Notes / Lessons Learned
- Prefer a single Node/version management strategy per machine:
  - Use Homebrew for a single system-wide Node (good for many dev setups).
  - Use nvm (or asdf) for per-project/per-version Node management.
  - Avoid keeping both activated in shell startup files simultaneously.
- Always back up rc files before editing them programmatically.
- Move (or rename) leftover directories to a backup location first; only delete after confirming everything works.
- Use a per-user npm prefix (e.g., ~/.npm-global) to avoid permission issues and the need for sudo on global npm installs.
- Verify which executables are on PATH with which -a before removing anything.
- Keep Homebrew-managed binaries first in PATH on Apple Silicon: ensure /opt/homebrew/bin is before other paths.
- If you switch to nvm later, re-add the nvm initialization lines to your rc file and install desired Node versions with nvm.
- Periodically run basic checks (which -a node npm npx, node -v, npm -v) after system changes or upgrades.
