---
name: eli5-docs
description: "Use when the user invokes /eli5-docs to generate ELI5 documentation for a codebase. Scans a project directory, analyzes architecture and patterns, and produces a single self-contained HTML file with plain-language explanations, Mermaid.js diagrams, and a dark-themed navigation UI. Targets vibe coders, jr devs, and MVP handoffs. Triggers on: 'eli5-docs', 'explain this codebase', 'generate documentation for vibe coders', 'ELI5 docs', 'document this project for handoff'."
version: 1.0.0
tools: Read, Glob, Grep, Bash, Write
---

# eli5-docs — Codebase Documentation for Vibe Coders

## Purpose

Generate a self-contained HTML documentation page that explains a codebase in plain language with visual diagrams. Designed for jr devs and vibe coders who need to understand what AI built them, and for MVP handoffs where the next developer needs to get up to speed fast.

---

## Step 0: Identify Target Directory

If the user provides a path argument (e.g., `/eli5-docs C:\Games\Roundtable`), use that path.
Otherwise, use the current working directory.

Validate:
- The directory exists
- It contains at least one source file

If invalid, stop and tell the user.

---

## Step 1: Scan the Codebase

### 1a. Collect source files

Use Glob to find files matching these patterns:
- `**/*.py`, `**/*.js`, `**/*.ts`, `**/*.jsx`, `**/*.tsx`
- `**/*.html`, `**/*.css`, `**/*.scss`
- `**/*.go`, `**/*.rs`, `**/*.java`, `**/*.rb`, `**/*.php`
- `**/*.json`, `**/*.yaml`, `**/*.yml`, `**/*.toml`
- `**/*.md` (README files only — skip others)

**Exclude** these directories and files:
- `node_modules/`, `__pycache__/`, `.git/`, `dist/`, `build/`, `.next/`, `.venv/`, `venv/`
- `.env`, `*.lock`, `*.min.js`, `*.min.css`, `*.map`, `*.pyc`
- Any file over 50KB (likely generated/bundled)

### 1b. Read dependency manifests first

Prioritize reading these files for context (if they exist):
- `package.json`, `requirements.txt`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`
- `README.md`

### 1c. Read source files

Read each source file. For files over 500 lines, read the first 300 lines (enough to understand structure, exports, and key functions).

### 1d. Size check

If the project has more than 100 source files, warn the user and ask whether to proceed with the full project or target a specific subdirectory.

---

## Step 2: Analyze the Codebase (Phase 1)

Using the full instructions in `prompts/analysis.md`, analyze all collected file contents and produce a structured analysis covering these 9 documentation sections:

1. **Project Overview** — What is this? What problem does it solve? Who is it for?
2. **Architecture** — How do the major components connect? (Mermaid flowchart)
3. **Tech Stack** — What technologies are used and WHY each was chosen?
4. **File-by-File Breakdown** — Each file's purpose explained in plain language
5. **Data Flow** — How does data move through the system? (Mermaid sequence diagram)
6. **Key Concepts** — Patterns and concepts a jr dev must understand
7. **API Reference** — HTTP endpoints/routes with plain-language descriptions (omit if none)
8. **How to Modify** — 3-5 most common changes someone would want to make
9. **Glossary** — Every technical term used, explained simply

Hold this analysis in memory for Phase 2. Do not save it to disk.

---

## Step 3: Generate HTML (Phase 2)

Using the full instructions in `prompts/html-report.md`, take the Phase 1 analysis and generate a complete, self-contained HTML document.

The HTML must include:
- Embedded CSS (dark theme)
- Mermaid.js via CDN for diagram rendering
- Google Fonts (Inter + JetBrains Mono) via CDN
- Fixed sidebar navigation
- All 9 documentation sections
- Expandable file cards using `<details>/<summary>`
- Print-friendly styles via `@media print`
- Mobile-responsive layout

---

## Step 4: Save and Confirm

Save the generated HTML as:
```
eli5-docs-{project-slug}-{YYYYMMDD}.html
```

Where:
- `{project-slug}` = directory name, lowercased, spaces replaced with hyphens
- `{YYYYMMDD}` = today's date

Save in the **project directory** (the target directory from Step 0).

### Confirmation output:

```
Done! eli5-docs generated.

  File: {filename}
  Project: {project name}
  Source files analyzed: {count}
  Sections: 9
  Diagrams: {count of mermaid diagrams}
  API endpoints documented: {count, or "N/A"}

Open the HTML file in any browser to view.
Ctrl+P for a print-friendly PDF export.
```

---

## Error Handling

- **No source files found**: Stop and tell the user the directory appears empty or contains no recognized source files.
- **Too many files (>100)**: Warn and ask the user to confirm or narrow scope to a subdirectory.
- **Mermaid syntax errors**: The HTML template includes a Mermaid error handler that shows a "Diagram could not render" fallback instead of a broken chart.
- **Binary/generated files**: Skip silently (the glob exclusions should handle this).
