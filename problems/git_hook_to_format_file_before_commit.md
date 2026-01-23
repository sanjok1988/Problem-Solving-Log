# Auto-Format Laravel Code with Pint Git Pre-Commit Hook (macOS)

## Date
2026-01-23

## Environment / Context
- **OS / Platform**: macOS (Sonoma/Ventura/Monterey)
- **PHP Version**: 8.2+ (with Composer)
- **Laravel Version**: 10/11 (any version with Composer)
- **Tools used**: Composer, Git, Laravel Pint, Terminal

## Problem Description
Laravel developers waste time manually running `pint` before commits or argue about code style. Committed code often violates **PSR-12** standards, causing inconsistent codebase.[`1`]

## Root Cause
No automated enforcement of code style during **git commit**. Developers forget to run `pint` or only format the entire project (`pint .`) instead of **just staged files**.[`4`]

## Solution

### **1. Install Laravel Pint**
```bash
composer require laravel/pint --dev
```

### **2. Create Hooks Directory**
```bash
cd /path/to/your/laravel/project
mkdir -p .git/hooks
```

### **3. Create Pre-Commit Hook**
```bash
touch .git/hooks/pre-commit
```

### **4. Add Script (Copy & Paste)**
```bash
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/sh

# Run Laravel Pint ONLY on staged PHP files
staged_files=$(git diff --cached --name-only --diff-filter=ACM -- '*.php')

if [ -n "$staged_files" ]; then
    echo "🔄 Running Laravel Pint on staged files..."
    ./vendor/bin/pint $staged_files -q
    git add $staged_files
    echo "✅ Code formatted and re-staged"
fi
EOF
```

### **5. Make Executable (macOS)**
```bash
chmod +x .git/hooks/pre-commit
```

### **6. Test It**
```bash
# Create bad code
echo '<?php echo "test";' > test.php
git add test.php
git commit -m "Test hook"
```
**Output:**
```
🔄 Running Laravel Pint on staged files...
✅ Code formatted and re-staged
[main abc1234] Test hook
```

## Notes / Lessons Learned
- **Only staged files** get formatted (faster than `pint .`)[`1`]
- **Auto-restages** fixed files so commit includes formatting[`4`]
- **Bypass**: `git commit --no-verify -m "emergency"`
- Works across **team members** (hooks are in `.git/` but commit easily)[`2`]
- **macOS specific**: `chmod +x` required, hooks run in Terminal/bash
- Add to README: "Code auto-formats on commit via Pint hook"
- Combine with **GitHub Actions** for CI enforcement[`4`]

**✅ Result**: Zero-config, team-enforced, **PSR-12 compliant commits** forever![`1`]

[`1`]: https://saasykit.com/blog/using-laravel-pint-with-git-pre-commit-hooks-to-automate-code-styling[`2`]: https://github.com/muffycompo/laravel-pint-pre-commit-hook[`3`]: https://laraveldaily.com/post/laravel-pint-pre-commit-hooks-github-actions[`4`]: https://dev.to/pavlosisaris/mastering-code-quality-in-laravel-pint-with-git-hooks-and-docker-365l

[`1`]: https://saasykit.com/blog/using-laravel-pint-with-git-pre-commit-hooks-to-automate-code-styling
[`2`]: https://github.com/muffycompo/laravel-pint-pre-commit-hook
[`3`]: https://dev.to/pavlosisaris/mastering-code-quality-in-laravel-pint-with-git-hooks-and-docker-365l
[`4`]: https://laraveldaily.com/post/laravel-pint-pre-commit-hooks-github-actions
[`5`]: https://www.youtube.com/watch?v=ooF6t2ITu58
[`6`]: https://codingwisely.com/blog/how-to-run-pint-and-pest-on-git-hooks-with-laravel-sail
[`7`]: https://paulisaris.com/mastering-code-quality-in-laravel-pint-with-git-hooks-and-docker/
