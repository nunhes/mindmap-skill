# Interactive Mindmap Generator

A React-based mindmap generator that creates interactive visualizations from OPML files with customizable color palettes.

## Quick Start

Run a lightweight static server from the project directory, then open the page in your browser:

```bash
# macOS / Linux
python3 -m http.server 3000

# Windows (PowerShell)
py -m http.server 3000

# Any platform with Node.js installed
npx serve .
```

After starting a server, visit `http://localhost:3000/index.html` (or the port reported in your terminal). Most browsers block `fetch()` from reading local files, so double‑clicking `index.html` will show an endless “Loading mind map…” message—use one of the commands above instead.

## Project Structure

```
mindmap-skills/
├── index.html                    # Main preview file
├── palette.xml                   # Color palette
├── mindmap.opml                  # Content structure
├── fonts/                        # Custom fonts
│   ├── hubot_sans.ttf
│   └── OFL.txt
├── SKILL.md                      # Claude skill instructions
├── LICENSE.txt                   # MIT License
└── README.md                     # This file
```

## Features

- **Interactive**: Click nodes to expand details, drag to rearrange
- **Data-driven**: Content from OPML, colors from XML palette
- **Hierarchical**: Supports nested information structures
- **Customizable**: Easy to modify colors, content, and fonts
- **No build step**: Pure HTML/JS/React via CDN

## Creating a Custom Mindmap

### 1. Edit Content (mindmap.opml)
```xml
<opml version="2.0">
  <head><title>Your Title</title></head>
  <body>
    <outline text="Root Topic" _note="Description">
      <outline text="Category" _note="Details">
        <outline text="Item" _note="More info"/>
      </outline>
    </outline>
  </body>
</opml>
```

### 2. Edit Colors (palette.xml)
```xml
<palette>
  <color name='Color-1' rgb='042940' r='4' g='41' b='64' />
  <color name='Color-2' rgb='005C53' r='0' g='92' b='83' />
  <!-- Add more colors -->
</palette>
```

### 3. Preview
Refresh your browser to see changes instantly!

## File Formats

### OPML Attributes
- `text` - Node label (keep short, 1-3 words)
- `_note` - Detailed description (shows in popup)

### Color Palette
- `name` - Color identifier
- `rgb` - Hex color code (without #)
- `r`, `g`, `b` - RGB values (0-255)

## Customization

### Change Font
1. Add your font file to `fonts/` directory
2. Update `@font-face` in `index.html`:
```css
@font-face {
  font-family: 'YourFont';
  src: url('./fonts/your-font.ttf') format('truetype');
}
```

### Adjust Layout
Edit the React component inside `index.html`:
- `baseRadius` and `distanceFromParent` control branch spacing
- Angular spread logic inside `processNode` balances siblings
- Drag behavior lives near the render section (search for `node-wrapper`)

## Best Practices

✅ **Do:**
- Keep 5-8 main categories
- Use 3-6 items per category
- Short labels (1-3 words)
- Detailed `_note` descriptions
- Harmonious color palette (5-8 colors)
- Max 3 levels of nesting

❌ **Don't:**
- Exceed 10 main categories (gets crowded)
- Create deeply nested structures (hard to read)
- Use similar colors (hard to distinguish)
- Forget to validate XML syntax

## Interactions

- **Click node**: Show/hide details popup
- **Drag node**: Reposition (children move with parent)
- **Alt+Click**: Toggle expand/collapse (nodes with children)
- **Alt+Click**: Toggle visibility (leaf nodes)
- **Scroll**: Zoom in/out
- **Drag background**: Pan view

## Troubleshooting

**Nothing appears:**
- Check browser console for errors
- Verify XML files are valid
- Ensure your local server is running (default examples use port 3000)

**Colors not showing:**
- Check `palette.xml` format
- Verify hex codes are valid (6 characters)
- Ensure at least 3-5 colors defined

**Nodes overlapping:**
- Reduce number of categories
- Adjust the spacing constants in `index.html`

**Drag not working:**
- Check browser console for React errors
- Try refreshing the page

## Submitting as a Claude Skill

When ready to submit as a Claude skill:

```bash
# Create a zip with all files
zip -r mindmap-skill.zip * -x "*.DS_Store" -x ".claude/*"
```

Submit the zip file with:
- SKILL.md (required - Claude instructions)
- All other files in this directory

## License

MIT License - See LICENSE.txt
Font license in fonts/OFL.txt

## Browser Compatibility

- Chrome/Edge 90+
- Firefox 88+
- Safari 14+

Requires ES6+ and modern DOM APIs (DOMParser, Fetch).
