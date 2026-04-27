# Environment Setup and Run Guide

This project uses a Python virtual environment to isolate dependencies. Below are the exact steps we used, the package versions, and how to run the app.

## Prerequisites
- Python 3.13 (any recent 3.9+ should work)
- macOS/Linux shell (examples use `zsh`/bash)

## 1) Create and activate the virtual environment
- Create venv: `python3 -m venv .venv`
- Activate (macOS/Linux): `source .venv/bin/activate`
- Upgrade tooling (recommended): `pip install --upgrade pip setuptools wheel`

## 2) Install project dependencies
- Install from requirements: `pip install -r requirements.txt`

## 3) Dependency versions used
The project pins the following in `requirements.txt`:
- `blinker==1.9.0`
- `click==8.2.1`
- `Flask==2.3.3`
- `itsdangerous==2.2.0`
- `Jinja2==3.1.6`
- `MarkupSafe==3.0.2`
- `Werkzeug==3.1.3`
- `Flask-WTF>=1.2.1,<2.0` (1.2.x recommended)
- `WTForms>=3.0` (3.x recommended)

Why the Flask‑WTF/WTForms pins: older Flask‑WTF (e.g., 1.1.1) imports `werkzeug.urls.url_encode`, which was removed in Werkzeug 3.x. With `Werkzeug==3.1.3`, you must use `Flask-WTF>=1.2.1` and WTForms 3.x.

Check installed versions:
```
python -c "import importlib.metadata as m; print('Flask', m.version('Flask')); print('Werkzeug', m.version('Werkzeug')); print('Flask-WTF', m.version('flask_wtf')); print('WTForms', m.version('WTForms'))"
```

## 4) Run the application
- From the project root with venv active:
  - `python main2.py`
- Open the app in your browser: `http://127.0.0.1:5000`

Alternative (Flask CLI):
```
export FLASK_APP=main2.py
flask run
```

## 5) Common maintenance
- Deactivate venv: `deactivate`
- Reinstall deps after edits to requirements: `pip install -r requirements.txt`
- Upgrade Flask‑WTF/WTForms explicitly (if needed):
  - `pip install -U "Flask-WTF>=1.2.1,<2.0" "WTForms>=3.0"`

## 6) Troubleshooting
- Error: `ImportError: cannot import name 'url_encode' from 'werkzeug.urls'`
  - Cause: Incompatible Flask‑WTF (old) with Werkzeug 3.x.
  - Fix: `pip install -U "Flask-WTF>=1.2.1,<2.0" "WTForms>=3.0"`

- Verify all versions match pins: rerun the version check command above.

## 7) Optional: lock exact versions
If you want a fully locked snapshot for reproducibility:
```
pip freeze --exclude-editable > requirements.lock.txt
```

