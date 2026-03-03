# Salesbuzz Support Knowledge Base

This repository contains structured support knowledge for the Salesbuzz system.

## Goals
- Capture incident resolutions in a consistent, searchable format.
- Help Application Support quickly diagnose and resolve recurring issues.
- Preserve evidence (logs/config/DB checks) and the verified resolution steps.

## How to use
- Start with **kb/_index/tags.md** to browse by tag.
- Browse incidents under **kb/incidents/** (real cases).
- Browse reusable procedures under **kb/runbooks/**.
- Browse error-centric entries under **kb/known-errors/**.

## How to add a new incident case
1. Create a file under `kb/incidents/` named:
   `YYYY-MM-DD-short-title.md`
2. Use the template in `kb/_templates/incident-template.md`.
3. Keep sensitive data out of the repo (no passwords, tokens, personal data). Replace customer names with aliases.

## Conventions
- Use UTC timestamps when possible.
- Prefer copy/paste of exact log lines (sanitized) and exact steps executed.
- Include verification steps and prevention/monitoring suggestions.

---
Maintained by Application Support.