# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

Run from the repo root. The venv lives at `venv/` (Windows layout: `venv\Scripts\`).

```bash
# Setup (Windows: use `python`, not `python3`)
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt

# Run dev server (port 5001, debug on)
python app.py

# Tests (pytest + pytest-flask are in requirements.txt; no tests/ directory exists yet)
pytest
pytest path/to/test_file.py::test_name   # single test
```

No linter, formatter, or build step is configured. There is no `package.json`; the front end is plain HTML, CSS, and vanilla JS served by Flask.

## Architecture

Flask app with a single module, `app.py`, and server-rendered Jinja2 templates. Persistence is planned as SQLite through `database/db.py`, but that file is still a stub (comments only).

**Routes.** All routes live in `app.py`. Implemented: `/` (landing), `/register`, `/login`, `/terms`. Routes that return plain-text placeholders, pending later steps: `/logout`, `/profile`, `/expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete`. Expect these to be replaced with real views, not extended as strings.

**Template inheritance.** `templates/base.html` is the shared layout: navbar, `main.main-content`, footer, then `static/js/main.js`. Child pages extend it and fill these blocks: `title`, `head` (extra `<head>` content), `content`, and `scripts` (page-specific JS, loaded after `main.js`). Footer links use `url_for(...)`, so route names are the contract; renaming a view function breaks every template that references it.

**Styling.** One stylesheet, `static/css/style.css`. Colours, fonts, radii, and widths are CSS custom properties on `:root` (`--ink`, `--paper`, `--accent`, `--accent-2`, `--font-display`, `--font-body`, `--max-width`, `--auth-width`, `--radius-*`). Reuse these rather than hard-coding values. Responsive breakpoints in use are 600px, 640px, 900px. Icons are inline SVG; no icon or UI library is loaded. Google Fonts (DM Serif Display, DM Sans) are loaded in `base.html`.

**Pages with inline code.** `landing.html` carries its own scoped `<style>` block and a `scripts` block for the "How it works" video modal. The modal is vanilla JS and stops playback by reassigning `iframe.src`. Keep page-specific behaviour in the template's `scripts` block unless it is shared across pages, in which case it goes in `static/js/main.js`.

**Static assets.** `static/js/main.js` is currently only the footer year. Students add JS there as features are built.

## Context and gotchas

- The site is a student project built in numbered steps (see the comments in `app.py` and `database/db.py`). Do not implement later-step features (expense CRUD, profile, logout) unless asked.
- `/register` and `/login` render forms but do not process credentials. The hero email form in `landing.html` submits with GET to `/register` and drops the value.
- `terms.html` and footer links contain placeholders: the contact email, the GitHub username in `base.html`, and the LinkedIn, Portfolio, Contact, and Privacy `href="#"` values.
- `venv/`, `__pycache__/`, `*.db`, and `.env` are gitignored. `file1.md` is a tracked scratch file, not documentation.
- Git on this Windows machine prints CRLF conversion warnings on commit. These are not errors.
