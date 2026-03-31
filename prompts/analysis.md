# Phase 1: Codebase Analysis Prompt

You are analyzing a codebase to produce structured documentation for someone who has never seen this code before — likely a junior developer, a vibe coder who used AI to build an MVP, or someone receiving a project handoff.

Your goal is to make the codebase **understandable**, not just described. Explain WHY things exist, not just WHAT they do. Use analogies a non-technical person could follow.

---

## Input

You have been given the contents of every source file in the project. You have also read any dependency manifests (package.json, requirements.txt, etc.) and README files.

---

## Output

Produce a structured analysis with exactly these 9 sections. Hold this in memory for Phase 2 (HTML generation). Do not save to disk.

### Section 1: Project Overview

- **what_it_does**: 2-3 sentences explaining what this project does, written so a non-developer could understand. Use a real-world analogy if helpful.
- **what_problem_it_solves**: Why does this exist? What would someone have to do manually without it?
- **who_its_for**: Who is the target user?

### Section 2: Architecture

- **mermaid_flowchart**: A valid Mermaid flowchart showing how the major components connect to each other. This is NOT a file tree — it shows logical relationships (e.g., "Browser sends request to Server, Server calls External API, API returns data, Server streams to Browser").
- **explanation**: A plain-language walkthrough of the diagram, 3-5 sentences.

**Mermaid syntax rules (CRITICAL — diagrams will break if violated):**
- Use `graph TD` (top-down) or `graph LR` (left-right) depending on what fits
- Node labels MUST use square brackets: `A["My Label"]` — NEVER parentheses
- Keep labels under 30 characters
- Use `-->` for arrows, `-->|"label"|` for labeled arrows
- Escape any special characters in labels (quotes, ampersands, angle brackets)
- Use subgraphs to group related components: `subgraph Name["Label"]`
- Limit to 8-15 nodes. More than that loses clarity.
- Test that every node ID is unique

### Section 3: Tech Stack

For each technology used, provide:
- **name**: The technology name
- **category**: One of: language, framework, library, service, tool, infrastructure
- **why**: A one-sentence explanation of why this technology was chosen (infer from context — e.g., "Flask because the app only needs a few API routes and doesn't need a full framework like Django")

### Section 4: File-by-File Breakdown

For each source file, provide:
- **path**: Relative path from project root
- **purpose**: One-liner (e.g., "The main server — handles all API requests")
- **eli5**: 2-4 sentences explaining what this file does, using analogies where helpful. Example: "Think of this file as the receptionist at a hotel — it takes incoming requests, figures out what room (function) to send them to, and makes sure the response gets back to the guest (browser)."
- **key_things**: 3-5 bullet points of the most important functions, classes, or exports in the file
- **lines**: Approximate line count

Order files by importance (entry points first, then core logic, then utilities, then config).

### Section 5: Data Flow

- **mermaid_sequence**: A valid Mermaid sequence diagram tracing ONE important user action end-to-end. Pick the most representative action (e.g., "user submits a form and gets a response", "user sends a message and receives a streamed AI reply").
- **explanation**: A plain-language walkthrough of what happens at each step.

**Mermaid sequence diagram rules:**
- Use `sequenceDiagram` (no graph declaration)
- Participants: `participant A as "Label"`
- Messages: `A->>B: Action description` (solid arrow) or `A-->>B: Response` (dashed arrow)
- Use `activate`/`deactivate` for long-running operations
- Use `Note over A,B: explanation` for clarifying notes
- Use `loop Label` / `end` for repeated operations
- Keep message labels short and clear
- Limit to 4-6 participants maximum

### Section 6: Key Concepts

Identify 3-7 patterns or concepts that a junior developer MUST understand to work with this codebase. For each:
- **concept**: The name (e.g., "Server-Sent Events", "State Management", "Middleware Pattern")
- **eli5**: Explain it like you're explaining to a smart 15-year-old. No jargon. Use analogies. 2-4 sentences.
- **where_used**: Which files use this concept

### Section 7: API Reference

If the project exposes HTTP endpoints (routes, API paths), document each one:
- **method**: GET, POST, PUT, DELETE, etc.
- **path**: The URL path (e.g., `/api/users`)
- **purpose**: Plain-language description of what this endpoint does
- **request**: What you send (body, query params) — or "Nothing" for simple GETs
- **response**: What you get back — described in plain language, not schema

If the project has NO HTTP endpoints, skip this section entirely and note "No API endpoints — this is not a web server."

### Section 8: How to Modify

Identify 3-5 of the most likely changes someone would want to make to this project. For each:
- **task**: What the developer wants to do (e.g., "Add a new AI model preset")
- **steps**: Numbered steps with specific file names and locations
- **files_to_touch**: List of files they'd need to modify

Think about what a new developer inheriting this project would want to customize first.

### Section 9: Glossary

List every technical term, acronym, or jargon used in the codebase that a junior developer might not know. For each:
- **term**: The word or phrase
- **definition**: A plain-language definition, 1-2 sentences max

Include framework-specific terms (e.g., "route", "middleware"), protocol terms (e.g., "SSE", "WebSocket"), and any domain-specific vocabulary.

Order alphabetically.

---

## Quality Checklist

Before proceeding to Phase 2, verify:
- [ ] Every Mermaid diagram uses square bracket labels only — NO parentheses
- [ ] All Mermaid node IDs are unique within their diagram
- [ ] The architecture diagram shows logical relationships, not file structure
- [ ] The data flow traces a single, complete user action
- [ ] ELI5 explanations use analogies and avoid unexplained jargon
- [ ] The glossary covers every technical term used in the documentation
- [ ] "How to Modify" tasks reference specific file paths
- [ ] API endpoints are listed with their HTTP methods
