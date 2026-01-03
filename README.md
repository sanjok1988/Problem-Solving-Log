
# Developer Problem-Solving Log

This repository is a personal knowledge log of **real-world development problems** I encountered and solved with the help of ChatGPT. Each entry documents the **original problem**, **root cause**, and **exact solution**, focusing on practical issues like environment setup, PHP/Laravel version conflicts, tooling bugs, configuration mistakes, and debugging workflows.

The goal is to preserve solutions that are easy to forget, help other developers facing similar issues, and encourage learning through practical troubleshooting.

---

## How to Use This Repository

* Browse the `problems/` folder to see solved problems.
* Each problem is documented in its own Markdown file.
* Problems are tagged for easy filtering (e.g., `PHP`, `Laravel`, `Env`, `Git`).

---

## Problem/Solution Template

When adding a new problem, use this format:

```markdown
# Problem Title

## Date
YYYY-MM-DD

## Environment / Context
- OS / Platform
- PHP / Node / Laravel / Framework version
- Tools used (Composer, Git, Docker, etc.)

## Problem Description
A brief explanation of the issue or error you encountered.

## Root Cause
What caused the issue, why it happened.

## Solution
Step-by-step explanation of the fix, commands, and configuration changes.

## Notes / Lessons Learned
Tips or insights for future reference.
```

---

## Example Entry

```markdown
# PHP Laravel Deprecation Warnings with PHP 8.5

## Date
2026-01-03

## Environment / Context
- macOS, ZSH
- PHP 8.5-dev
- Laravel 10
- Homebrew PHP

## Problem Description
Deprecation warnings appeared for Laravel framework and vendor files due to PHP 8.5 changes.

## Root Cause
System PHP was upgraded to 8.5; Laravel/vendor code was not fully compatible with new PHP nullable parameter and PDO constants rules.

## Solution
1. Installed PHP 8.3 via Homebrew.
2. Configured `.envrc` for direnv to prepend PHP 8.3 paths.
3. Added `eval "$(direnv hook zsh)"` to `~/.zshrc`.
4. Ran `direnv allow` and verified `which php` points to PHP 8.3.

## Notes / Lessons Learned
Always ensure project-specific PHP versions are prioritized using direnv to prevent deprecation warnings on older frameworks.
```

---

This structure makes your repo **easy to navigate**, **searchable**, and **educational** for other developers.

