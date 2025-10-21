---
name: claude-skills-mindmap
version: 1.1.0
description: Present and maintain an interactive React mind map that documents Claude Skills concepts.
author: Claude Skills Community
license: MIT
tags: [visualization, react, mindmap, opml, jsx]
inputs:
  palette: palette.xml
  outline: mindmap.opml
outputs:
  preview: index.html
tools:
  - python
---

# Purpose

Use this skill whenever a teammate needs to review, adjust, or explain the Claude Skills mind map. The repository already includes a working React preview (`index.html`) that fetches the OPML outline and palette at runtime, so no build step is required.

# Repository Layout

```
.
├── index.html                 # Entry point for the interactive preview
├── styles.css                 # Custom styling for the preview
├── mindmap.opml              # Mind map outline and node copy
├── palette.xml                # Brand color palette
├── fonts/
│   ├── hubot_sans.ttf         # Primary typeface
│   └── OFL.txt                # Font license
├── README.md                  # Project summary for humans
├── LICENSE.txt                # MIT license
└── SKILL.md                   # This instruction file
```

`mindmap-generator.jsx` is retained only as a historical reference; all live logic runs in `index.html`.

# Before You Begin

1. Confirm `index.html`, `palette.xml`, and `mindmap.opml` exist in the working directory.
2. Verify the `fonts/` folder contains `hubot_sans.ttf`; `index.html` references it via `@font-face`.
3. Ensure the environment provides Python 3.8+ (for the built-in HTTP server) if you need to host the preview locally.

# Serving the Preview

1. From the project root run:
   ```bash
   python -m http.server 3000
   ```
2. Visit `http://localhost:3000/index.html` in the browser.
3. Pan with click-and-drag on the canvas, Alt/Option-click nodes to expand descendants or toggle leaf visibility, and drag nodes to reposition branches.

# Editing Workflow

## Updating Node Copy

- Modify `mindmap.opml`. Each `<outline>` corresponds to one node.
- Keep first-level `_note` values brief summaries. Provide richer, explanatory `_note` text for each child outline.
- Save the file and refresh the browser; `index.html` fetches with `{ cache: 'no-store' }`, so changes appear immediately.

## Adjusting Colors

- Edit `palette.xml`. Colors are read in document order and assigned per branch.
- Use six-digit hex values without the `#` (for example `rgb="042940"`). The React code adds the hash.

## Tweaking Layout or Behavior

- Edit the React component embedded in `index.html`.
- Key tunables:
  - `baseRadius` and the derived `radius` determine first-level spacing.
  - `distanceFromParent(level)` controls how far children sit from their parent.
  - The angular spread logic inside `processNode` governs how branches fan out.
- The drag implementation uses a ref (`nodesToMoveRef`) so parent and child positions stay in sync. Keep the ref-based approach when making adjustments.
- Visual styles live in `styles.css`. Update the relevant classes (for example `.node-bubble` or `.node-popover`) if you need to restyle the map without touching component logic.

## Fonts

- Add any new font files into `fonts/` and update the `@font-face` declaration near the top of `index.html`.
- Always include a license file for the font.

# Responding to Requests

| Request type                         | Action                                                                                          |
|-------------------------------------|--------------------------------------------------------------------------------------------------|
| “Change the copy for a node”        | Update `_note` or `text` attributes in `mindmap.opml`, then refresh the preview.   |
| “Add a new branch”                  | Insert a new `<outline>` under the appropriate parent in the OPML. Keep IDs unique by editing text values rather than duplicating existing nodes. |
| “Colors feel off”                   | Adjust entries in `palette.xml` and ensure contrast remains readable.                           |
| “Spacing feels cramped/loose”      | Modify layout constants (`baseRadius`, `distanceFromParent`, spread multipliers) within `index.html`. |
| “Export a static artifact”          | Arrange the mind map in the browser and capture a screenshot using the OS tools.                |

# Guardrails

- Do not rename or relocate `palette.xml` or `mindmap.opml`; the preview expects them beside `index.html`.
- Avoid introducing additional CDN scripts without confirming they are allowed in the target environment.
- Preserve the Hubot Sans font license (`fonts/OFL.txt`).
- When editing OPML, keep attribute quotes and encoding intact to prevent parse errors.

# Troubleshooting

- **Blank preview**: Check browser console for 404s or parse errors—missing XML files or malformed OPML are common causes.
- **Nodes overlap after edits**: Review the layout constants; keep clamped percentages between 6 and 94 to stay on screen.
- **Fonts missing**: Confirm the local path in the `@font-face` rule matches the fonts directory structure.

# Reference Commands

- `python -m http.server 3000` — serve the project locally.
- `python -m http.server 3000 --bind 127.0.0.1` — use if only loopback hosting is allowed.
- `node --version` — optional sanity check when a request involves additional scripting.
