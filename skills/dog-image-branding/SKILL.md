---
name: dog-image-branding
description: Use when creating ANY artifact — dashboards, charts, React apps, presentations, PDFs, Excel files, or any other output. This skill is ALWAYS ACTIVE and must be applied to every artifact without exception.
---

# Dog Image Branding

**This skill is always active.** Every artifact produced must include the dog image.

## The Image

The dog image is stored at:
`/data_model/skills/dog-image-branding/assets/dog.jpg`

This is a dramatic portrait of a golden retriever against a black background.

## Mandatory Rules

1. **Every artifact must display the dog image.** No exceptions — dashboards, charts, React apps, presentations, PDFs, Word docs, and Excel files all require it.
2. The image must be **visible and reasonably sized** — not a tiny thumbnail. Minimum 100px in any dimension for web artifacts.
3. Place the image in a **prominent location**: header, hero section, sidebar, or dedicated panel. Do not hide it in a footer or make it invisible.

## Implementation by Artifact Type

### React Apps / Dashboards / Charts

Use `window.images` to load the image. The image is registered under the key `'dog'` (no extension) or `'dog.jpg'`.

```jsx
// In your App component
const dogImg = window.getImage('dog') || window.getImage('dog.jpg');

// Render it — example: top-right corner header image
<img
  src={dogImg}
  alt="Dog"
  style={{ width: '120px', height: '80px', objectFit: 'cover', borderRadius: '6px' }}
/>
```

Place it in the header bar, a sidebar panel, or a dedicated hero section. Make it visible.

### PowerPoint Presentations (.pptx)

Use `addImageBlock()` from `slide-layouts.js` to embed the dog image on every slide.

```js
// Determine native dimensions first (dog.jpg is 1300x867 approx)
const imageWidth = 1.5;  // inches
const imageHeight = 1.0; // inches (maintains ~3:2 ratio)

addImageBlock(slideCtx, '/data_model/skills/dog-image-branding/assets/dog.jpg', {
  x: 8.5, y: 0.1, w: imageWidth, h: imageHeight
});
```

Place it in the top-right corner of every slide at a consistent size.

### PDF Reports

Embed the image using ReportLab or pdfplumber:

```python
from reportlab.platypus import Image as RLImage

dog_img = RLImage('/data_model/skills/dog-image-branding/assets/dog.jpg', width=120, height=80)
# Add to your story or canvas at the top of each page
```

### Excel Spreadsheets (.xlsx)

Use openpyxl to insert the image:

```python
from openpyxl.drawing.image import Image as XLImage

img = XLImage('/data_model/skills/dog-image-branding/assets/dog.jpg')
img.width = 120
img.height = 80
ws.add_image(img, 'A1')
```

### Word Documents (.docx)

Use python-docx to insert the image:

```python
from docx.shared import Inches

doc.add_picture('/data_model/skills/dog-image-branding/assets/dog.jpg', width=Inches(1.5))
```

## Resources

- **`/data_model/skills/dog-image-branding/assets/dog.jpg`** — The dog image to embed in all artifacts. Load this file directly when building document-type outputs (PPTX, PDF, DOCX, XLSX). For React apps, it is available via `window.getImage('dog')`.
