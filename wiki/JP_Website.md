# JP Website Workflow

## Folder Structure

Images are organized into two category folders within `images/`:

```
images/
├── paragliding/
│   ├── paragliding001.jpg
│   ├── paragliding002.jpg
│   ├── ...
│   ├── paragliding010_thumb.png  (video thumbnail)
│   └── paragliding010.mov        (video file)
├── motorcycling/
│   ├── motorcycling001.jpg
│   ├── motorcycling002.jpg
│   └── ...
└── JP_Launching.png              (unused/archive)
```

## Naming Convention

All images follow a **sequential naming format**:
- **Paragliding**: `paragliding001.jpg`, `paragliding002.jpg`, etc.
- **Motorcycling**: `motorcycling001.jpg`, `motorcycling002.jpg`, etc.
- **Video**: `category###.mov` (video file) with `category###_thumb.png` (thumbnail)

This ensures consistent naming, easy scanning, and predictable ordering in the gallery.

## Image Compression Workflow

### Before Adding Images

All photos must be compressed to approximately **0.5MB** before adding to the gallery. This keeps file sizes manageable while maintaining visual quality.

### Compression Process

1. **Prepare photos** — Collect images in the `images/` folder (can be any naming initially)
2. **Convert HEIC to JPG** (if needed) — Use your phone, Mac, or online tools to convert Apple's HEIC format to standard JPG
3. **Compress using Node.js script**:

```bash
cd C:\Users\User\OneDrive\Documents\ClaudeCodeFolder\JP_WebPage

node compress-new.js
```

The script (`compress-new.js` or similar) uses the **sharp** Node.js library to:
- Start with quality level 80
- Iteratively reduce quality by 10 until file size ≤ 500 KB
- Apply progressive JPEG encoding for faster loading
- Preserve EXIF rotation data

**Target size**: ~0.5 MB per image (can be slightly over for very large/complex photos)

### Example Output

```
Processing: IMG_4284.jpg (1.38MB)...
  Quality 80: 1.54MB
  Quality 70: 1.37MB
  Quality 60: 1.29MB
  ...
  Quality 20: 0.66MB
  ✓ Done: 0.66MB
```

## Adding New Images

### Step 1: Prepare Images
- Copy photos to `images/` folder
- Ensure they're in JPG format (convert HEIC if needed)

### Step 2: Compress
```bash
node compress-new.js
```

### Step 3: Organize & Rename
1. Create/verify subfolders exist:
   ```bash
   mkdir -p images/paragliding images/motorcycling
   ```

2. Move files to appropriate folder:
   ```bash
   mv image.jpg images/motorcycling/
   ```

3. Rename sequentially:
   - Check the latest number in the folder (e.g., `motorcycling018.jpg`)
   - Rename new image to next number (e.g., `motorcycling019.jpg`)
   - Use format: `category###.jpg` (zero-padded)

### Step 4: Update gallery.html

Edit `gallery.html` and locate the appropriate `configs` section:

**Motorcycling example:**
```javascript
motorcycling: {
  title: 'Adventure Motorcycling',
  items: [
    'images/motorcycling/motorcycling001.jpg',
    'images/motorcycling/motorcycling002.jpg',
    'images/motorcycling/motorcycling019.jpg'  // Add new image here
  ]
}
```

**Paragliding example (with video):**
```javascript
paragliding: {
  title: 'Paragliding',
  items: [
    'images/paragliding/paragliding001.jpg',
    // ... other images ...
    { type: 'video', thumb: 'images/paragliding/paragliding010_thumb.png', src: 'images/paragliding/paragliding010.mov' }
  ]
}
```

For video items, use the format:
```javascript
{ type: 'video', thumb: 'images/paragliding/paragliding###_thumb.png', src: 'images/paragliding/paragliding###.mov' }
```

### Step 5: Preview & Verify

Start the local server:
```bash
node server.js
```

Open browser:
- Motorcycling: `http://localhost:8000/gallery.html?type=motorcycling`
- Paragliding: `http://localhost:8000/gallery.html?type=paragliding`

Verify all images load correctly.

### Step 6: Commit & Push

```bash
git add gallery.html images/
git commit -m "Add new images"
git push origin main
```

## Tools & Dependencies

- **Node.js** — runtime for compression script
- **sharp** — Node.js image processing library (install via `npm install sharp`)
- **gallery.html** — displays galleries via URL query parameter (`?type=motorcycling` or `?type=paragliding`)

## Key Files

- `gallery.html` — main gallery page with embedded `configs` object
- `images/motorcycling/` — motorcycling photos
- `images/paragliding/` — paragliding photos
- `compress-new.js` or similar — image compression utility (created during workflow)
