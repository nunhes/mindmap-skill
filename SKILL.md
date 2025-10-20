---
name: mindmap-generator
version: 1.0.0
description: Generate interactive React mindmaps from OPML files with custom color palettes
author: Claude Skills Community
license: MIT
tags: [visualization, react, mindmap, opml, jsx]
---

# Mindmap Generator Skill

This skill enables Claude to generate interactive, draggable mindmaps from OPML files using React. The mindmaps feature custom color palettes defined in XML and support hierarchical data visualization.

## Overview

The Mindmap Generator creates engaging visual representations of hierarchical data stored in OPML format. Each mindmap is:
- **Interactive**: Click nodes to expand details, drag to rearrange
- **Customizable**: Colors come from palette.xml
- **Responsive**: Adapts to different screen sizes
- **Font-aware**: Uses custom fonts from the fonts/ directory

## File Structure

When generating a mindmap skill, you'll work with these files:

```
mindmap-skills/
├── SKILL.md                      # This file (metadata and instructions)
├── mindmap-generator.jsx         # Main React component (reads palette and OPML)
├── palette.xml                   # Color palette definition
├── claude-skills-mindmap.opml   # Content structure (hierarchical data)
├── fonts/                        # Custom fonts directory
│   ├── hubot_sans.ttf
│   └── OFL.txt
└── preview.html                  # Development preview (not for submission)
```

## Input File Formats

### palette.xml
Defines colors used throughout the mindmap:

```xml
<palette>
  <color name='Verde-1' rgb='193C40' r='24' g='59' b='63' />
  <color name='Verde-2' rgb='2E5902' r='45' g='89' b='1' />
  <!-- More colors... -->
</palette>
```

### OPML Structure
Hierarchical content with `text` and `_note` attributes:

```xml
<opml version="2.0">
  <head>
    <title>Your Mindmap Title</title>
  </head>
  <body>
    <outline text="Root Node" _note="Main description">
      <outline text="Child Node 1" _note="Details about this topic">
        <outline text="Subnode" _note="More specific information"/>
      </outline>
      <outline text="Child Node 2" _note="Another topic"/>
    </outline>
  </body>
</opml>
```

## Key Implementation Details

### Component Structure

The main component (`MindMapGenerator`) should:

1. **Load and parse palette.xml** on mount
   - Extract colors using DOMParser
   - Store in state as array of {name, rgb} objects

2. **Load and parse OPML** on mount (after colors loaded)
   - Extract title from `<head><title>`
   - Parse root outline for center node
   - Parse child outlines for surrounding nodes
   - Calculate positions in a circular layout around center

3. **Support interaction**
   - Click to select/deselect nodes
   - Drag to reposition nodes
   - Hover for visual feedback

4. **Render structure**
   - SVG layer for connecting lines
   - Positioned divs for node bubbles
   - Popup cards with detailed information

### Color Assignment

Colors from palette.xml should be assigned to nodes in order:
- **Center node**: Always solid black (#000000) with white text
- **Connection lines**: Use second color from palette
- **Child nodes**: Cycle through all palette colors
  - White background with 3px solid colored border
  - Text color matches border color

In description popups:
- Main heading (h3) uses the node's color
- Border-top separator uses the node's color
- Child item labels use the node's color
- Body text remains gray for readability

### Position Calculation

Child nodes should be positioned in a circle around the center:

```javascript
const radius = 35; // percentage units
const angleStep = (2 * Math.PI) / childOutlines.length;
const x = centerX + radius * Math.cos(angle);
const y = centerY + radius * Math.sin(angle);
```

### State Management

Required state:
- `nodes`: Object map of all node data
- `nodePositions`: Current positions (updated during drag)
- `connections`: Array of {from, to} pairs
- `colors`: Parsed palette colors
- `selectedNode`: Currently selected node ID
- `hoveredNode`: Currently hovered node ID
- `draggingNode`: Node being dragged (if any)

## Styling Guidelines

- Use Tailwind CSS for styling
- Font: Custom font from fonts/ directory (with fallbacks)
- Background: Light emerald (`bg-emerald-50`), no white container
- **Center node**: Black background with white text, rounded pill
- **Child nodes**: White background with 3px colored border, text matches border color
- Detail cards: White with border and shadow, colored accents
- Transitions: Smooth scaling and position changes
- Connection lines: Semi-transparent, colored from palette

## Development Preview

For development, use `preview.html` which loads:
1. React and ReactDOM from CDN
2. Babel standalone for JSX transformation
3. Tailwind CSS from CDN
4. Custom font face declaration
5. The mindmap-generator.jsx component

**Important**: `preview.html` is for local development only. Do NOT include it in the skill submission zip file.

## Submission Package

When ready to submit, create a zip file containing ONLY:
- SKILL.md
- mindmap-generator.jsx
- palette.xml
- claude-skills-mindmap.opml
- fonts/ directory (with all font files)

## Usage Instructions

When a user asks you to create a mindmap:

1. **Understand the topic**: Ask what subject they want to visualize
2. **Determine structure**: Identify main categories (5-8 recommended)
3. **Choose colors**: Suggest a color palette or let them provide one
4. **Choose font**: Ask if they want to use a custom font or the default
5. **Generate files**:
   - Create palette.xml with color definitions (5-8 colors recommended)
   - Create OPML file with hierarchical content structure (use descriptive filename like `project-management.opml`)
   - If custom font requested: Add to fonts/ directory and update preview.html font-face
   - Copy mindmap-generator.jsx and update fetch URLs to match the new filenames
6. **Preview setup**: Start local server (`python3 -m http.server 8000`)
7. **Test**: User opens `http://localhost:8000/preview.html` in browser
8. **Iterate**: Adjust colors, content, or layout based on feedback

### Customizing for Different Projects

**Different color palette:**
- Create new palette.xml (or rename like `my-colors.xml`)
- Update fetch URL in mindmap-generator.jsx: `fetch('./my-colors.xml')`

**Different content/topic:**
- Create new OPML file (e.g., `marketing-strategy.opml`)
- Update fetch URL in mindmap-generator.jsx: `fetch('./marketing-strategy.opml')`

**Different font:**
- Add font files to fonts/ directory
- Update @font-face in preview.html:
  ```css
  @font-face {
    font-family: 'YourFont';
    src: url('./fonts/your-font.ttf') format('truetype');
  }
  body {
    font-family: 'YourFont', -apple-system, ...;
  }
  ```

**Complete custom setup:**
Just update these lines in mindmap-generator.jsx:
```javascript
// Line 18: Color palette
fetch('./your-palette.xml')

// Line 35: Content structure
fetch('./your-topic.opml')
```

### Quick Commands for Testing

```bash
# Start local server
python3 -m http.server 8000

# Then open: http://localhost:8000/preview.html
```

### Creating New Mindmaps

The beauty of this skill is that you only need to modify the data files:
- **palette.xml** - Change colors
- **[topic].opml** - Change content and structure
- **mindmap-generator.jsx** - No changes needed (unless customizing behavior)

Simply update the fetch URLs in mindmap-generator.jsx if using a different OPML filename.

## Best Practices

- **Keep it simple**: OPML should have 1 root with 5-8 children max for readability
- **Meaningful colors**: Use colors that make semantic sense (e.g., related topics use similar hues)
- **Clear hierarchy**: Root → Main topics → Subtopics (3 levels recommended)
- **Concise text**: Node labels should be 1-3 words; details go in `_note` attribute
- **Font licensing**: Only include properly licensed fonts

## Common Issues

- **Overlapping nodes**: Adjust radius value or reduce number of child nodes
- **Text contrast**: Verify `isLightColor()` threshold works for your palette
- **Large popups**: Limit child nodes to 5-6 items per parent
- **Font not loading**: Check path in preview.html matches fonts/ directory
- **OPML not parsing**: Validate XML structure, ensure proper closing tags

## Example Workflow

### Scenario 1: User wants a custom mindmap from scratch

```
User: "Create a mindmap about project management methodologies with a blue/green color scheme"

Claude's workflow:
1. Create project-management.opml with structure:
   - Root: "Project Management"
   - Children: "Agile", "Waterfall", "Scrum", "Kanban", "Lean"
   - Grandchildren: Key practices for each

2. Create blue-green-palette.xml with 5-6 blue/green colors

3. Copy mindmap-generator.jsx and update:
   - Line 18: fetch('./blue-green-palette.xml')
   - Line 35: fetch('./project-management.opml')

4. User starts server and previews

5. Iterate based on feedback
```

### Scenario 2: User wants to use different font

```
User: "Use Roboto font instead"

Claude's workflow:
1. Ask user to provide Roboto font files or download from Google Fonts
2. Add roboto.ttf to fonts/ directory
3. Update preview.html @font-face:
   font-family: 'Roboto';
   src: url('./fonts/roboto.ttf')
4. Update body font-family: 'Roboto', ...
5. User refreshes browser to see new font
```

### Scenario 3: User asks Claude to compose content

```
User: "Create a mindmap teaching me about machine learning. You choose the structure."

Claude's workflow:
1. Research/compose ML topics (Supervised, Unsupervised, Deep Learning, etc.)
2. Create machine-learning.opml with Claude-generated content
3. Suggest color palette (e.g., tech-themed: blues, purples, greens)
4. Create tech-colors.xml
5. Update mindmap-generator.jsx fetch URLs
6. User previews and provides feedback
7. Claude iterates on content/structure
```

## Technical Requirements

- React 18+
- Modern browser with ES6+ support
- DOMParser API for XML parsing
- Fetch API for loading files

This skill empowers Claude to create beautiful, functional mindmaps that users can interact with and customize to their needs.
