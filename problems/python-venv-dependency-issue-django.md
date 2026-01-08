# Problem Title
Python Virtual Environment & Dependency Resolution Failure in Django Project

## Date
2026-01-08

---

## Environment / Context

**OS / Platform**
- macOS (Apple Silicon)

**Language / Framework**
- Python 3.11 (initial – problematic)
- Python 3.10.x (final – working)
- Django
- LangChain ecosystem

**Tools Used**
- Python `venv`
- pip
- pyenv
- Homebrew
- Git

---

## Problem Description

While setting up a Django project with multiple dependencies (Django REST Framework, LangChain, ML/NLP libraries), repeated errors occurred such as:

- `ModuleNotFoundError: No module named 'django'`
- `ModuleNotFoundError: No module named 'rest_framework_simplejwt'`
- `pip list` showing almost no installed packages
- Dependency resolution failures during `pip install -r requirements.txt`

Despite the virtual environment being activated correctly, required packages were not installed or detected.

---

## Root Cause

1. **Python Version Incompatibility**
   - The project was using **Python 3.11**.
   - Many dependencies explicitly required Python `<3.10` or `<3.11`.
   - pip correctly refused to install incompatible packages, resulting in an empty environment.

2. **Invalid Dependency for macOS**
   - `pysqlite3-binary` was included in `requirements.txt`.
   - This package has no compatible distribution for macOS + Python 3.11 and is unnecessary because SQLite is bundled with Python on macOS.

3. **Forced Installation Flags Misuse**
   - Use of `--ignore-installed` and `--force-reinstall` masked the real issue.
   - pip could not override Python version constraints.

---

## Solution

### Step 1: Install Compatible Python Version (3.10)

```bash
brew install pyenv
pyenv install 3.10.14
pyenv local 3.10.14

Verify:

python --version


⸻

Step 2: Remove Broken Virtual Environment

deactivate
rm -rf venv


⸻

Step 3: Create New Virtual Environment

python -m venv venv
source venv/bin/activate


⸻

Step 4: Fix requirements.txt
	•	Remove the following line:

pysqlite3-binary


⸻

Step 5: Install Dependencies (Without Force Flags)

pip install --upgrade pip
pip install -r requirements.txt


⸻

Step 6: Verify Setup

pip list
python -m django --version


⸻

Notes / Lessons Learned
	•	Always verify Python version compatibility before installing dependencies.
	•	Avoid using --ignore-installed and --force-reinstall unless absolutely necessary.
	•	Do not include OS-specific or unnecessary packages (e.g., pysqlite3-binary on macOS).
	•	Python 3.10 remains the most stable choice for Django + ML/NLP-heavy projects.
	•	A clean virtual environment is often faster than debugging a broken one.

⸻


