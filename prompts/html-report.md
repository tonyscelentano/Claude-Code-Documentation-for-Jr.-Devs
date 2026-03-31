# Phase 2: HTML Report Generation Prompt

You are a frontend engineer generating a self-contained, interactive HTML documentation page.

You have a structured codebase analysis from Phase 1 covering 9 documentation sections. Generate a COMPLETE, single-file HTML document with embedded CSS and JavaScript. The only external dependencies are Google Fonts and Mermaid.js via CDN.

---

## DESIGN SYSTEM

Use these exact values as CSS custom properties:

```css
:root {
  /* Colors */
  --bg: #0a0a0b;
  --surface: #12121a;
  --surface-2: #1a1a25;
  --surface-3: #222233;
  --border: #2a2a3a;
  --border-light: #3a3a4a;
  --text: #e0e0e8;
  --text-muted: #8888a0;
  --text-dim: #555568;
  --accent: #6c8cff;
  --accent-dim: #4a6adf;
  --accent-glow: rgba(108, 140, 255, 0.15);
  --green: #4ade80;
  --yellow: #fbbf24;
  --red: #f87171;
  --purple: #a78bfa;
  --cyan: #22d3ee;

  /* Typography */
  --font-sans: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  --font-mono: 'JetBrains Mono', 'Fira Code', 'Cascadia Code', monospace;
  --t-xs: 0.75rem;
  --t-sm: 0.875rem;
  --t-md: 1rem;
  --t-lg: 1.25rem;
  --t-xl: 1.5rem;
  --t-2xl: 2rem;
  --t-3xl: 2.5rem;

  /* Spacing */
  --s-xs: 4px;
  --s-sm: 8px;
  --s-md: 16px;
  --s-lg: 24px;
  --s-xl: 32px;
  --s-2xl: 48px;
  --s-3xl: 64px;

  /* Layout */
  --sidebar-width: 260px;
  --content-max: 900px;
  --radius: 8px;
  --radius-lg: 12px;
}
```

---

## LAYOUT

### Structure

```
+--sidebar--+---main content area (scrollable)---+
| project   | section 1                           |
| name      | section 2                           |
| ________  | section 3                           |
| nav links | ...                                 |
| ________  | section 9                           |
| meta info |                                     |
+-----------+-------------------------------------+
```

### Sidebar (fixed, left, 260px)

- **Project name** at top, styled as `--t-xl`, font-weight 700, color `--accent`
- **"eli5-docs"** label below in `--text-dim`, `--t-xs`, uppercase tracking
- Horizontal rule (`--border`)
- **Navigation links**, one per section:
  - Prefix each with a subtle section number in `--text-dim` (01, 02, ... 09)
  - Link text is the section title
  - Active link gets `--accent` color and a left border accent
  - On click: smooth scroll to section
- **Bottom area** (pushed to bottom via flex):
  - "Generated {date}" in `--text-dim`
  - "{n} files analyzed" in `--text-dim`
  - "eli5-docs v1.0" in `--text-dim`

### Main Content Area

- `margin-left: var(--sidebar-width)`
- `max-width: var(--content-max)` centered within remaining space
- `padding: var(--s-2xl) var(--s-xl)`
- Each section is a `<section>` with an `id` matching the nav link href

---

## SECTION RENDERING

### Section 1: Project Overview

- Large heading: project name in `--t-3xl`
- Subtitle: "What is this?" in `--text-muted`
- The ELI5 description in a prominent card (`--surface` background, `--border` border, `--radius-lg`)
- Below: two smaller cards side-by-side — "Problem it solves" and "Who it's for"

### Section 2: Architecture

- Heading: "Architecture"
- The Mermaid flowchart in a `<pre class="mermaid">` block, centered
- Below: the plain-language explanation in a `--surface` card

### Section 3: Tech Stack

- Heading: "Tech Stack"
- Grid of cards (2 columns on desktop, 1 on mobile)
- Each card shows:
  - Technology name (bold, `--text`)
  - Category badge (small pill: language=`--accent`, framework=`--purple`, library=`--cyan`, service=`--yellow`, tool=`--green`, infrastructure=`--red`)
  - "Why?" explanation in `--text-muted`

### Section 4: File-by-File Breakdown

- Heading: "Files"
- Each file as a `<details>` element (collapsed by default):
  - `<summary>`: file path in monospace + one-liner purpose
  - Expanded content:
    - ELI5 explanation
    - "Key things in this file:" followed by bullet list
    - Line count badge

### Section 5: Data Flow

- Heading: "Data Flow"
- Brief intro: "Here's what happens when [action]:"
- The Mermaid sequence diagram in a `<pre class="mermaid">` block
- Below: step-by-step plain-language walkthrough

### Section 6: Key Concepts

- Heading: "Key Concepts"
- Each concept as a card with:
  - Concept name as card title
  - ELI5 explanation as body
  - "Used in:" followed by file paths as inline code badges

### Section 7: API Reference

- Heading: "API Reference"
- If no endpoints: show a muted note "This project doesn't expose HTTP endpoints."
- If endpoints exist: a styled table with columns:
  - Method (GET/POST/etc. in colored badge — GET=`--green`, POST=`--accent`, PUT=`--yellow`, DELETE=`--red`)
  - Path (monospace)
  - What it does
- Each row expandable (click to see request/response details)

### Section 8: How to Modify

- Heading: "Common Modifications"
- Each modification as a numbered card:
  - Task title (bold)
  - Numbered steps
  - "Files you'll touch:" with file paths as inline code badges

### Section 9: Glossary

- Heading: "Glossary"
- Two-column layout on desktop (single on mobile)
- Each term: bold term name, followed by definition
- Alternating subtle background for readability (`--surface` / `--surface-2`)

---

## MERMAID INTEGRATION

```html
<script src="https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.min.js"></script>
<script>
  mermaid.initialize({
    startOnLoad: true,
    theme: 'dark',
    themeVariables: {
      primaryColor: '#1a1a25',
      primaryTextColor: '#e0e0e8',
      primaryBorderColor: '#6c8cff',
      lineColor: '#6c8cff',
      secondaryColor: '#222233',
      tertiaryColor: '#12121a',
      fontFamily: 'Inter, sans-serif',
      fontSize: '14px'
    },
    flowchart: { curve: 'basis', padding: 20 },
    sequence: { actorMargin: 50, messageMargin: 40 }
  });

  // Error handler — show fallback instead of broken diagram
  mermaid.parseError = function(err, hash) {
    const el = document.querySelector('.mermaid-error');
    if (el) el.style.display = 'block';
  };
</script>
```

Each Mermaid diagram should be wrapped in:
```html
<div class="diagram-container">
  <pre class="mermaid">
    [diagram code here]
  </pre>
  <p class="mermaid-error" style="display:none; color: var(--yellow); font-style: italic;">
    Diagram could not render. View page source for the raw diagram code.
  </p>
</div>
```

---

## JAVASCRIPT (inline, at bottom of body)

Minimal vanilla JS for:

1. **Smooth scroll navigation**: Sidebar links scroll to sections with `behavior: 'smooth'`

2. **Active section highlighting**: Use `IntersectionObserver` to highlight the current section's nav link:
   ```js
   const observer = new IntersectionObserver((entries) => {
     entries.forEach(entry => {
       if (entry.isIntersecting) {
         document.querySelectorAll('.nav-link').forEach(l => l.classList.remove('active'));
         document.querySelector(`.nav-link[href="#${entry.target.id}"]`)?.classList.add('active');
       }
     });
   }, { rootMargin: '-20% 0px -70% 0px' });
   document.querySelectorAll('section[id]').forEach(s => observer.observe(s));
   ```

3. **Mobile sidebar toggle** (screen width < 768px):
   - Hamburger button (three lines) fixed top-left
   - Clicking toggles sidebar visibility (slide in/out with transform)
   - Overlay backdrop when sidebar is open
   - Close on link click (auto-scroll to section)

4. **Expandable API table rows**: Click a row to toggle request/response details below it.

---

## PRINT STYLES

```css
@media print {
  :root {
    --bg: #ffffff;
    --surface: #f5f5f5;
    --surface-2: #eeeeee;
    --border: #cccccc;
    --text: #111111;
    --text-muted: #555555;
    --accent: #2255cc;
  }
  .sidebar { display: none !important; }
  .main { margin-left: 0 !important; max-width: 100% !important; }
  .mobile-toggle { display: none !important; }
  section { break-inside: avoid; page-break-inside: avoid; }
  h2 { page-break-after: avoid; }
  details { open: true; } /* Doesn't work in CSS — JS below handles it */
  .diagram-container { break-inside: avoid; }
}
```

Add to the JS: on `beforeprint` event, open all `<details>` elements. On `afterprint`, restore original state.

---

## GOOGLE FONTS

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
```

---

## ADDITIONAL CSS RULES

- `* { box-sizing: border-box; margin: 0; padding: 0; }`
- `body { background: var(--bg); color: var(--text); font-family: var(--font-sans); line-height: 1.6; }`
- `code, .mono { font-family: var(--font-mono); font-size: var(--t-sm); }`
- Inline code: `background: var(--surface-2); padding: 2px 6px; border-radius: 4px;`
- Cards: `background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: var(--s-lg);`
- Scrollbar styling for webkit: thin, `--surface-2` track, `--border-light` thumb
- Smooth scroll: `html { scroll-behavior: smooth; }`
- Selection color: `::selection { background: var(--accent-glow); color: var(--text); }`

---

## OUTPUT

Return ONLY the complete HTML document starting with `<!DOCTYPE html>`. No explanations, no markdown fences, no commentary. Just the HTML.
