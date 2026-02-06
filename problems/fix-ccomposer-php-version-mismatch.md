# Composer dependencies require PHP >= 8.4.0 (server running 8.2.28)

## Date
2026-02-06

## Environment / Context
- OS / Platform: Deployment server (Linux)
- PHP / Framework: PHP 8.2.28 (runtime), Composer-managed PHP app (e.g., Laravel)
- Tools used: Composer, CI/CD pipeline

## Problem Description
Composer failed during install/update with:

`Composer detected issues in your platform: Your Composer dependencies require a PHP version ">= 8.4.0". You are running 8.2.28.`

## Root Cause
At least one dependency version in the resolved dependency set requires $$PHP \ge 8.4$$, but the server runtime is $$PHP = 8.2.28$$. This commonly happens when `composer.lock` was generated/updated on a newer PHP version (or without a platform constraint), then deployed to a server with older PHP.

## Solution
Two valid paths:

### A) Upgrade PHP on the server (recommended if possible)
1. Upgrade server runtime to $$PHP \ge 8.4$$ (ensure CI uses the same).
2. Install dependencies:
```bash
composer install --no-dev
```

### B) Keep PHP 8.2 and adjust dependencies
1. On a machine using PHP 8.2, enforce the Composer platform:
```bash
composer config platform.php 8.2.28
```
2. Update dependencies to versions compatible with PHP 8.2:
```bash
composer update
```

## Notes / Lessons Learned
- Use `composer install` in CI/CD to install exactly what’s in `composer.lock`.
- Align PHP versions across dev/CI/prod, or set `platform.php` to prevent lockfiles that require a higher PHP than production.
- To identify the blocking package in the future:
  - `composer why-not php 8.2.28`
  - `composer check-platform-reqs`