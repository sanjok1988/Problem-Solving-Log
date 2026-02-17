# Homebrew Bash Killed Running Script

  ## Date

  2026-02-17

  ## Environment / Context

  - OS / Platform: macOS (Apple Silicon, arm64)
  - PHP / Node / Laravel / Framework version: Not applicable
  - Tools used (Composer, Git, Docker, etc.): Homebrew, bash, zsh

  ## Problem Description

  Running bash ./postgres-setup.sh returned zsh: killed     bash ./postgres-setup.sh even though the script
  itself was a simple echo "done".
  cannot run code ., valet links, cursor ., .sh files

  ## Root Cause

  The Homebrew bash binary at /opt/homebrew/bin/bash was being killed by macOS (SIGKILL). This was not a
  script issue; it was the shell binary itself. Reinstalling Homebrew bash fixed it.

  ## Solution

  1. Reinstall Homebrew bash:

     brew reinstall bash
  2. Re-test the script using Homebrew bash:

     /opt/homebrew/bin/bash /Users/sanjokdangol/Projects/postgres-setup.sh
     Result: done, exit code 0.
  3. Run the original command:

     bash ./postgres-setup.sh

  ## Notes / Lessons Learned

  - If zsh: killed appears when running a script, check whether the shell binary is being terminated rather
    than the script itself.
  - Reinstalling the Homebrew shell can resolve macOS SIGKILL issues on unsigned or corrupted binaries.