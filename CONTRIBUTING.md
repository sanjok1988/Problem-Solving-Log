

## Quick-Start Guide for Contributors

We welcome contributions from developers who want to share problems they solved! Follow these steps to add your solution:

1. **Copy the template**

   ```bash
   cp problems/template.md problems/<descriptive-name>.md
   ```

   Replace `<descriptive-name>` with a clear, short identifier for your problem (e.g., `php-laravel-deprecation`).

2. **Fill out all sections** in the new Markdown file:

   * **Problem Title**
   * **Date**
   * **Environment / Context**
   * **Problem Description**
   * **Root Cause**
   * **Solution**
   * **Notes / Lessons Learned**

3. **Optional**: Add screenshots, logs, or commands to `assets/` and link them in your Markdown.

4. **Commit and push** your contribution:

   ```bash
   git add problems/<descriptive-name>.md
   git commit -m "Add solution for <problem-title>"
   git push origin main
   ```

5. **Create a Pull Request** on GitHub if you want your solution reviewed before merging.

---

### Tips for Contributors

* Use clear and concise language; assume readers are developers with basic knowledge.
* Include **commands and config changes** wherever relevant.
* Make your problem **reproducible**, so others can understand the context.
