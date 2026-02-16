
# Starship Prompt Setup for Laravel Dev (macOS)

## Date
2026-02-16

## Environment / Context
- OS / Platform: macOS (zsh or bash)
- PHP / Node / Laravel / Framework version: PHP (varies), Node (varies), Laravel (per-project via `artisan`)
- Tools used (Composer, Git, Docker, etc.): Git, PHP CLI, Node.js, Python, Starship

## Problem Description
Need a clean terminal prompt for Laravel development that shows:
- Git branch/status + current directory
- Laravel project info (Laravel version, env, debug, db)
- Runtime versions (PHP, Node, Python)

## Root Cause
Default shell prompt doesn’t surface Laravel/project/runtime context, so you lose time checking versions and `.env` settings manually.

## Solution

### 1) Install Starship
**Homebrew (recommended on macOS):**
```sh
brew install starship
```

**Verify:**
```sh
starship --version
```

### 2) Enable Starship in your shell

**zsh (default on macOS):**
```sh
echo 'eval "$(starship init zsh)"' >> ~/.zshrc
source ~/.zshrc
```

**bash (if you use bash):**
```sh
echo 'eval "$(starship init bash)"' >> ~/.bashrc
source ~/.bashrc
```

### 3) Add the config
Create the config directory + file:
```sh
mkdir -p ~/.config
nano ~/.config/starship.toml
```

Paste this config:
```toml
add_newline = true
command_timeout = 3000

format = """
$os$username$directory$git_branch$git_status$line_break$custom$php$nodejs$python$character
"""

[os]
disabled = false
format = "[$symbol ]($style)"
style = "bold"
[os.symbols]
Macos = ""

[username]
show_always = true
format = "[$user]($style) "
style_user = "bold"

[directory]
format = "[📁 $path]($style) "
style = "bold cyan"
truncation_length = 3
truncate_to_repo = true

[git_branch]
symbol = "⎇ "
format = "[${symbol}$branch]($style) "
style = "bold blue"

[git_status]
format = "[$all_status$ahead_behind]($style) "
style = "bold yellow"
conflicted = "✖"
ahead = "↑"
behind = "↓"
diverged = "↕"
untracked = "?"
stashed = "*"
modified = "!"
staged = "+"
renamed = "»"
deleted = "✘"

# --------------------
# Laravel-only modules
# --------------------

[custom.laravel]
description = "Laravel version"
detect_files = ["artisan"]
command = "php artisan --version 2>/dev/null | awk '{print $3}'"
format = "[Laravel $output]($style) "
style = "bold red"

[custom.laravel_env]
description = "APP_ENV"
detect_files = ["artisan", ".env"]
command = "awk -F= '/^APP_ENV=/{print $2; exit}' .env 2>/dev/null"
format = "[env:$output]($style) "
style = "bold yellow"

[custom.laravel_debug]
description = "APP_DEBUG"
detect_files = ["artisan", ".env"]
command = "awk -F= '/^APP_DEBUG=/{print $2; exit}' .env 2>/dev/null"
format = "[debug:$output]($style) "
style = "bold purple"

[custom.laravel_db]
description = "DB_CONNECTION"
detect_files = ["artisan", ".env"]
command = "awk -F= '/^DB_CONNECTION=/{print $2; exit}' .env 2>/dev/null"
format = "[db:$output]($style) "
style = "bold cyan"

# --------------------
# Runtime versions
# --------------------

[php]
symbol = "🐘 "
format = "[$symbol$version]($style) "
style = "bold magenta"

[nodejs]
symbol = "⬢ "
format = "[$symbol$version]($style) "
style = "bold green"

[python]
symbol = "🐍 "
format = "[$symbol$version]($style) "
style = "bold cyan"

[character]
success_symbol = "[❯](bold green)"
error_symbol   = "[❯](bold red)"
```

### 4) Reload your terminal
Open a new terminal tab/window, or reload your shell config:
```sh
source ~/.zshrc
```

## Notes / Lessons Learned
- Laravel modules show only inside Laravel projects because they detect `artisan` (and `.env` where needed).
- If `.env` is missing (common in repos), the env/debug/db badges will simply not show.
- If the Apple symbol doesn’t render in your terminal font, change `Macos = ""` to `Macos = "Mac"`.
- If prompt feels slow, remove some `custom.*` blocks or raise `command_timeout`.