---
schema_version: 1
name: mindmap-skill
version: 1.1.0
description: Present and maintain an interactive React mind map that documents Claude Skills concepts.
authors:
  - Claude Skills Community
license: MIT
tags:
  - visualization
  - react
  - mindmap
  - opml
  - jsx
inputs:
  - id: palette
    path: palette.xml
  - id: outline
    path: mindmap.opml
outputs:
  - id: preview
    path: index.html
tools:
  - python
---

# Purpose

Use this skill whenever someone needs to review, adjust, or document the Claude Skills mind map. The preview lives in `index.html` and reads data from `mindmap.opml` and `palette.xml`, so keeping those sources up to date is the core task.

# Repository Layout

```
.
├── index.html                 # Entry point for the interactive preview
├── styles.css                 # Custom styling for the preview
├── mindmap.opml               # Mind map outline and node copy
├── palette.xml                # Branch color palette
├── fonts/
│   ├── hubot_sans.ttf         # Primary typeface
│   └── OFL.txt                # Font license
├── README.md                  # Human-facing project guide
├── LICENSE.txt                # MIT license
└── SKILL.md                   # This instruction file
```

# Standard Response Flow

1. **Clarify the request.** Confirm which nodes, branches, or colors need attention and gather the exact text the user wants.
2. **Apply the change.**
   - Update hierarchy or copy in `mindmap.opml`. Each `<outline>` element is a node (`text` = label, `_note` = detail).
   - Adjust branch colors in `palette.xml` (hex values without the `#`).
   - Modify layout or styling in `index.html` / `styles.css` if the request is about spacing or presentation.
3. **Report back.** Summarize what you changed, highlight any assumptions, and point the user to the preview instructions so they can load the latest map.

# Editing Reference

- **mindmap.opml** – Organizes nodes. Keep top-level `_note` values concise and use child `_note`s for richer explanations.
- **palette.xml** – Ordered list of branch colors. The React code prepends `#`.
- **index.html** – Contains the React component. Key tunables: `baseRadius`, `distanceFromParent`, and the angular spread logic in `processNode`.
- **styles.css** – Houses the visual design (node bubbles, popovers, background, etc.).
- **fonts/** – Add new fonts here and update the `@font-face` block in `index.html`. Always keep license files.

# Preview Instructions (share with the user)

```bash
# macOS / Linux
python3 -m http.server 3000

# Windows (PowerShell)
py -m http.server 3000

# Any platform with Node.js installed
npx serve .
```

Open `http://localhost:3000/index.html` and refresh after editing files. Browsers block local `fetch()` calls, so double-clicking `index.html` will not load the data.

# Guardrails

- Keep `palette.xml` and `mindmap.opml` beside `index.html`; the preview depends on these relative paths.
- Avoid adding new external CDNs unless the user confirms they are acceptable.
- Preserve licensing info in `fonts/OFL.txt` (and any additional font licenses you add).
- Maintain valid XML syntax when editing `mindmap.opml` or `palette.xml`.

# Troubleshooting Tips

- **Blank preview** – Check the browser console for 404s or XML parse errors.
- **Overlapping nodes** – Adjust spacing constants in `index.html` (`baseRadius`, `distanceFromParent`, angular spread multipliers).
- **Fonts missing** – Verify the `@font-face` path matches files in `fonts/`.

# Packaging Reminder

When the user wants to distribute the skill, zip the project root (excluding OS junk files) so the archive contains:
- `SKILL.md`
- `index.html`
- `styles.css`
- `mindmap.opml`
- `palette.xml`
- `fonts/` directory with licenses
