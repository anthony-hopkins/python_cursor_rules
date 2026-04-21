# cursor_rules — Python + Jinja2 + HTMX (Cursor configuration)

This repository holds a **single Cursor rules file** (`.cursorrules`) tuned for **enterprise-oriented Python web development**: server-rendered HTML with **Jinja2**, **HTMX** for interactivity without a JavaScript framework, and AI guidance toward **stdlib-first** code, **security-minded** middleware, and **consistent documentation**.

There is no application runtime here—only editor and AI context configuration you can copy into a project or keep as a reference template.

---

## Table of contents

- [What this file does](#what-this-file-does)
- [Requirements](#requirements)
- [Installation](#installation)
- [File format](#file-format)
- [Rule sections (overview)](#rule-sections-overview)
- [Snippets](#snippets)
- [AI behavior (high level)](#ai-behavior-high-level)
- [Customization](#customization)
- [Relationship to `.cursor/rules/`](#relationship-to-cursorrules)
- [Maintenance](#maintenance)
- [License](#license)

---

## What this file does

When placed at the **workspace root** as `.cursorrules`, Cursor can merge the declared rules for:

| Concern | What you get |
|--------|----------------|
| **Language / file typing** | HTML-like files mapped to `jinja-html`; `*.py` mapped to Python with stack-specific AI notes. |
| **Autocomplete** | HTMX attribute names suggested in relevant contexts. |
| **Snippets** | Tab-triggered expansions for Jinja2 and HTMX patterns. |
| **AI behavior** | Priority-ordered instructions for the assistant (enterprise Python, CSS theme, README/comments, security middleware). |
| **AI context** | Extra notes on templates, HTMX, and safe rendering. |

The header of `.cursorrules` documents the **field reference** (`id`, `description`, `patterns`, `language`, `ai_context`, `autocomplete`, `snippets`, `ai_behavior`, etc.) and recommends keeping `ai_behavior.rules` **concise** so instructions stay effective.

---

## Requirements

- **Cursor** (or an environment that reads project-level `.cursorrules` the same way—behavior can vary by product version; treat this repo as a **template** and verify in your Cursor version if something does not apply).

No Python interpreter, package manager, or build step is required to *use* these rules—they are configuration only.

---

## Installation

1. **Clone or copy** this repository (or copy only `.cursorrules`).
2. Put **`.cursorrules`** in the **root** of the project where you want these conventions.
3. **Restart or reload** the workspace in Cursor if rules do not appear immediately.

To use the same rules in **another repo**, copy `.cursorrules` into that repo’s root, or symlink it if you maintain a single canonical file elsewhere.

---

## File format

The file is **YAML** with a single top-level key `rules`, whose value is a **list of rule objects**.

- Lines starting with `#` are **YAML comments**; they are **not** automatically sent to the AI unless the same text appears inside `ai_context.notes` or `ai_behavior.rules` string blocks.
- Each rule entry typically includes an **`id`** (kebab-case, unique) for grep-friendly editing.

### Field reference (per rule entry)

| Field | Role |
|-------|------|
| `id` | Unique string (kebab-case recommended). |
| `description` | Short human summary (optional but recommended). |
| `patterns` | Glob list; when present, scopes file-type / context rules. |
| `language` | Editor language id (e.g. `python`, `jinja-html`). |
| `ai_context` | Extra model context: `enabled`, optional `weight`, `notes` (multiline). |
| `autocomplete` | Structured completions (e.g. HTML attribute names for HTMX). |
| `snippets` | List of `{ trigger, body }` for tab-triggered expansions. |
| `ai_behavior` | Instructions to the AI: `priority` (`high` \| `medium` \| `low`), `rules` (multiline, often markdown bullets). |

---

## Rule sections (overview)

### File type and language mapping

- **`python-jinja-filetypes`** — Treats many HTML-like extensions as Jinja2 templates (`*.jinja`, `*.jinja2`, `*.html.j2`, `*.j2`, `*.tpl`, `*.html`) with `language: jinja-html` and notes on `{{ }}`, `{% %}`, filters, macros, and safety (`\| e` vs `\| safe`).
- **`python-source-files`** — `*.py` with notes favoring stdlib (asyncio, ssl, secrets, pathlib, dataclasses, typing) and Jinja2 integration without unnecessary heavy frameworks.

### Jinja2 semantics (global AI context)

- **`python-jinja-syntax`** — Reinforces logic-light, data-driven templates: `extends` / `block` / `include`, small partials, explicit escaping.

### HTMX autocomplete

- **`htmx-attributes`** — Suggests attributes such as `hx-get`, `hx-post`, `hx-target`, `hx-trigger`, `hx-swap`, `hx-boost`, `hx-indicator`, and others, with AI notes to prefer server-rendered partials and HTMX over custom JavaScript.

### Snippets

Documented in detail under [Snippets](#snippets).

### AI behavior — stack, UI, docs

- **`ai-behavior-python-enterprise`** (high) — Expert enterprise Python; stdlib for crypto/logging/TLS/concurrency; Jinja2 for HTML; no JS; HTMX for interactivity.
- **`ai-behavior-css-aws-theme`** (medium) — CSS guidance with an AWS-like palette (e.g. background `#252F3E`, button accent `#FF9900`), responsive and accessible UI.
- **`ai-behavior-readme-and-comments`** (high) — Keep README.md in sync with code changes; document functionality, setup, and architecture; comment files thoroughly and consistently.

### AI behavior — security and middleware

- **`ai-behavior-security-middleware`** (high) — Encourages:

  - **Rate limiting** — Deterministic, testable logic (`time.monotonic()`, threading or asyncio primitives), IP or token buckets, correct HTTP status (e.g. 429), stdlib-first where possible.
  - **CSRF** — `secrets` for tokens, appropriate cookie flags, validation on mutating methods, double-submit or hidden-token patterns; no CSRF logic dependent on JavaScript.
  - **TLS** — `ssl` module or server docs, TLS 1.2+, strong ciphers / reverse proxy, HSTS, HTTP→HTTPS redirect; avoid self-signed certs except local dev.
  - **Middleware** — Composable, stateless when possible, explicit and testable; ASGI/WSGI stacking as appropriate; `logging` instead of `print` in request paths.

### AI context — rendering and HTMX responses

- **`ai-context-template-rendering`** — Jinja2 (or framework-integrated Jinja2), partials for HTMX, no JavaScript, plain dicts/dataclasses/small view models, safe HTML defaults.

---

## Snippets

Placeholders use **`${n:label}`** tab-stop style; **`${0}`** is often the final cursor position.

### Jinja2 (`python-jinja-snippets`)

| Trigger | Purpose |
|---------|---------|
| `jiif` | `{% if %} … {% endif %}` |
| `jifor` | `{% for %} … {% endfor %}` |
| `jiblock` | `{% block %} … {% endblock %}` |
| `jiextends` | `{% extends "base.html" %}` |
| `jiinclude` | `{% include "partial.html" %}` |
| `jimacro` | `{% macro name(args) %} … {% endmacro %}` |

### HTMX (`htmx-snippets`)

| Trigger | Purpose |
|---------|---------|
| `hxbtn` | Button with `hx-${1:get}`, target, swap |
| `hxform` | `hx-post` form with target and `innerHTML` swap |
| `hxload` | `div` with `hx-get`, `revealed` trigger, `innerHTML` swap |

---

## AI behavior (high level)

Rules are **priority-ordered** when instructions could conflict (`high` > `medium` > `low` in intent). In practice:

1. **Security, README/comments, and enterprise Python** guidance are emphasized strongly.
2. **Visual theme** (AWS-like colors) is medium priority—adjust or remove if your product has a different design system.
3. **Templates + HTMX** are reinforced globally so the assistant does not default to SPA-style JavaScript for this stack.

---

## Customization

- **Rename or split IDs** — Keep `id` values unique; grep the file before editing.
- **Tighten or loosen `patterns`** — If `*.html` should not always be Jinja in a given repo, narrow globs or override in a project-specific rules file.
- **Snippets** — Change triggers to match your muscle memory; keep `${n}` tab stops consistent.
- **CSS theme** — Replace AWS colors in `ai-behavior-css-aws-theme` with your brand tokens.
- **Security section** — Align with your org’s approved libraries and infrastructure (e.g. API gateway rate limits vs in-app buckets).

---

## Relationship to `.cursor/rules/`

Cursor also supports **Markdown rules** under **`.cursor/rules/`** (`.mdc` files with frontmatter). This repository intentionally uses a **root `.cursorrules`** YAML list as a **single-file, portable** bundle.

You may:

- Keep **only** `.cursorrules` in a repo, or
- Migrate concepts into `.cursor/rules/*.mdc` for finer-grained toggling, or
- Use **both**—avoid contradictory duplicates; prefer one source of truth per concern.

See Cursor’s current documentation for how your version merges legacy `.cursorrules` with project rules.

---

## Maintenance

- After **functional or architectural changes** to a codebase that uses these rules, update **README.md** and comments as the embedded `ai-behavior-readme-and-comments` rule suggests.
- When upgrading **Cursor**, re-verify that **snippets**, **language IDs**, and **rule merging** behave as expected.

---

## License

No `LICENSE` file is included in this template repository. If you publish a fork, add a license that fits your use case.

---

## Quick reference — repository layout

```text
cursor_rules/
├── .cursorrules   # YAML rules for Cursor (this project’s entire configuration)
├── .gitignore     # Python, venvs, OS, editors, secrets, and common junk
└── README.md      # This file
```

For questions about Cursor’s rule formats and best practices, see Cursor’s documentation on **Project Rules** and **`.cursorrules`**.
