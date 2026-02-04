# Git Master Branch Protection Setup

## Date
2026-02-04

## Environment / Context
- OS / Platform: macOS
- Git version: 2.x+
- Tools used: Terminal, GitHub, Git Hooks
- Repository hosting: GitHub (applicable to GitLab, Bitbucket with modifications)

## Problem Description
Need to enforce a strict workflow where all code changes must flow through a `dev` branch before reaching `master`. This prevents accidental pushes to production, ensures code review, and maintains a clean release process.

**Issues without protection:**
- Developers accidentally push directly to `master`
- No control over what reaches production
- Difficult to maintain release consistency
- Risky deployments without proper testing

## Root Cause
By default, Git allows anyone with push access to commit directly to any branch. Without server-side and local protection mechanisms:
- No automated enforcement of workflow
- Relies solely on developer discipline
- Easy to bypass in rush or by mistake
- No audit trail or approval requirements

## Solution

### Part 1: Server-Side Protection (GitHub)

1. **Navigate to Repository Settings**
   - Open repository → Settings tab → Branches (left sidebar)

2. **Add Branch Protection Rule**
   ```
   Branch name pattern: master
   ```

3. **Enable Required Options**
   ```
   ✅ Require a pull request before merging
      └─ Require approvals: 1 (or more)
   ✅ Require status checks to pass before merging
   ✅ Require branches to be up to date before merging
   ✅ Restrict who can push to matching branches
      └─ Allow: Administrators only (or specific users)
   ✅ Dismiss stale pull request approvals when new commits are pushed
   ```

4. **Click "Create" to Save**

### Part 2: Local Git Hooks (macOS)

1. **Create Hooks Directory**
   ```bash
   cd /path/to/your/project
   mkdir -p .githooks
   ```

2. **Create Pre-Push Hook File**
   ```bash
   touch .githooks/pre-push
   ```

3. **Add Protection Script**
   ```bash
   nano .githooks/pre-push
   ```
   
   Paste this content:
   ```bash
   #!/bin/bash
   
   # Prevent direct push to master
   protected_branches=("master" "main")
   current_branch=$(git rev-parse --abbrev-ref HEAD)
   
   for branch in "${protected_branches[@]}"; do
       if [[ "$current_branch" == "$branch" ]]; then
           echo ""
           echo "❌ ERROR: Cannot push directly to '$branch'"
           echo ""
           echo "✅ Correct workflow:"
           echo "   1. git checkout -b feature/your-feature"
           echo "   2. git push origin feature/your-feature"
           echo "   3. Create PR to 'dev' on GitHub"
           echo "   4. After merge to dev, create PR from dev → master"
           echo ""
           exit 1
       fi
   done
   
   exit 0
   ```

4. **Save File** (Nano: `Ctrl+X` → `Y` → `Enter`)

5. **Make Script Executable**
   ```bash
   chmod +x .githooks/pre-push
   ```

6. **Configure Git to Use Hooks**
   ```bash
   git config core.hooksPath .githooks
   ```

7. **Verify Configuration**
   ```bash
   git config core.hooksPath
   # Expected output: .githooks
   ```

### Part 3: Documentation

1. **Create Contributing Guide**
   ```bash
   cat > CONTRIBUTING.md << 'EOF'
   # Contributing Guidelines
   
   ## Workflow
   All changes flow: feature → dev → master
   
   ### How to Contribute
   
   1. **Create Feature Branch**
      ```bash
      git checkout dev
      git pull origin dev
      git checkout -b feature/your-feature-name
      ```
   
   2. **Make Changes**
      ```bash
      git add .
      git commit -m "feat: description"
      git push origin feature/your-feature-name
      ```
   
   3. **Create Pull Request**
      - Feature branch → dev
      - Request review
      - Wait for approval
      - Merge to dev
   
   4. **Release to Master**
      - Create PR: dev → master
      - Get approval
      - Merge to master
   
   ## Branch Naming
   - Feature: `feature/user-login`
   - Bug: `bugfix/fix-error`
   - Hotfix: `hotfix/critical-bug`
   
   ## Rules
   ❌ Never push directly to master or dev
   ✅ Always create feature branches from dev
   ✅ Always use pull requests for review
   EOF
   ```

2. **Commit Files**
   ```bash
   git add CONTRIBUTING.md .githooks/pre-push
   git commit -m "docs: add contribution guidelines and git hooks"
   git push origin dev
   ```

### Part 4: Team Setup Script

1. **Create Setup Script**
   ```bash
   cat > setup-hooks.sh << 'EOF'
   #!/bin/bash
   
   echo "🔧 Setting up Git hooks..."
   git config core.hooksPath .githooks
   echo "✅ Git hooks configured!"
   echo ""
   echo "Testing configuration..."
   git_hook_path=$(git config core.hooksPath)
   echo "Hooks path: $git_hook_path"
   echo ""
   echo "✅ Setup complete! You're ready to go."
   EOF
   ```

2. **Make Executable**
   ```bash
   chmod +x setup-hooks.sh
   ```

3. **Share with Team**
   ```bash
   git add setup-hooks.sh
   git commit -m "chore: add hook setup script"
   git push origin dev
   ```
   
   **Tell team**: `./setup-hooks.sh`

### Part 5: Testing

1. **Test Correct Workflow (Should Succeed)**
   ```bash
   git checkout dev
   git pull origin dev
   git checkout -b feature/test
   echo "test" > test.txt
   git add test.txt
   git commit -m "test: add test file"
   git push origin feature/test
   # ✅ Should succeed
   ```

2. **Test Protection (Should Fail)**
   ```bash
   git checkout master
   git push origin master
   # ❌ Should show: "ERROR: Cannot push directly to 'master'"
   ```

### Part 6: Troubleshooting Commands

```bash
# Check if hooks are configured
git config core.hooksPath

# View hook file
cat .githooks/pre-push

# Edit hook file
nano .githooks/pre-push

# Make hook executable (if needed)
chmod +x .githooks/pre-push

# Check hook permissions
ls -la .githooks/pre-push
# Should show: -rwxr-xr-x

# Remove hook protection (if needed)
git config --unset core.hooksPath

# List all git config values
git config --list | grep core
```

## Notes / Lessons Learned

### Key Insights
1. **Server-side vs Local Protection**
   - Server-side (GitHub branch protection) = Hard enforcement
   - Local hooks = Developer convenience + prevention
   - Both needed for complete protection

2. **Hook Limitations**
   - Local hooks can be bypassed: `git push --no-verify`
   - Server-side rules cannot be bypassed
   - Use both for best security

3. **Team Onboarding**
   - Provide `setup-hooks.sh` script for consistency
   - Share `CONTRIBUTING.md` to avoid confusion
   - Test the setup before pushing to main repo

### Best Practices
- ✅ Always create feature branches from `dev`, not `master`
- ✅ Use meaningful branch names: `feature/`, `bugfix/`, `hotfix/`
- ✅ Commit meaningful messages
- ✅ Create PRs for code review before merging
- ✅ Keep `.githooks` directory in version control
- ✅ Document workflow in `CONTRIBUTING.md`

### Common Mistakes
- ❌ Forgetting to run `git config core.hooksPath .githooks` on new clone
- ❌ Not making pre-push executable (`chmod +x`)
- ❌ Using `git push --no-verify` to bypass hooks
- ❌ Pushing to `master` directly when `dev` exists
- ❌ Not waiting for PR approval before merging

### Workflow Diagram
```
┌─────────────────────────┐
│   PRODUCTION (master)   │
│   (Protected Branch)    │
└────────────┬────────────┘
             │ (PR only from dev)
    ┌────────▼─────────┐
    │  STAGING (dev)   │
    │ (Code reviewed)  │
    └────────┬─────────┘
             │ (PR from features)
    ┌────────▼──────────────┐
    │ feature/new-feature   │
    │ (Development work)    │
    └───────────────────────┘
```

### Future Enhancement Ideas
- Add GitHub Actions to enforce commits from PRs only
- Setup automatic deployment from `master` branch
- Configure status checks (linting, tests) before merge
- Add commit message validation hooks
- Setup branch naming conventions check
- Auto-delete feature branches after merge

### References
- Git Hooks Documentation: https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks
- GitHub Branch Protection: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches
- Conventional Commits: https://www.conventionalcommits.org/
