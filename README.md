# TerraGoat Labs

Purpose: a safe sandbox to run TerraGoat (or other Goat projects), map progress with screenshots, and capture lessons learned.

## Structure
- /labs - each lab has its own folder with a `lab-report.md`, `screenshots/`, and `commands.log`.
- /diary - daily micro-progress notes.
- /lessons - consolidated lessons learned.
- /scripts - helper scripts for setup/cleanup.
- /screenshots - optional cross-lab screenshots.

## How to use
1. Create a new lab folder under `labs/` named like `terragoat-<module>`.
2. Copy the lab template: `labs/terragoat-<module>/lab-report.md`
3. Save screenshots into `labs/terragoat-<module>/screenshots/` and reference them in the report with markdown.

## Naming convention (recommended)
- Screenshots: `YYYYMMDD-HHMMSS_<short-desc>.png`
- Diary files: `YYYY-MM-DD.md`
- Lab folders: `terragoat-<module-name>`

## Tips
- Use `git lfs` for image files (this repo tracks screenshots with LFS).
- Keep every lab self-contained and include a short `commands.log` of commands you ran.
