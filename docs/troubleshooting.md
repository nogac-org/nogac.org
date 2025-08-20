# Troubleshooting: Common Issues and Diagnostic Solutions

This guide helps you diagnose and fix common problems in the VitePress index page system. Each issue includes symptoms, diagnostic steps, and specific solutions.

## Section Display Issues

### Issue: Section Not Appearing on Home Page

**Symptoms:**
- Section directory exists but tile doesn't appear
- Home page loads but missing expected sections
- No error messages in console

**Diagnostic Steps:**
```bash
# 1. Check if index.md exists
ls -la content/your-section/index.md

# 2. Verify frontmatter syntax
head -10 content/your-section/index.md

# 3. Check for hidden status
grep "hidden:" content/your-section/index.md

# 4. Test content discovery
node -e "
const { createContentLoader } = require('vitepress');
const loader = createContentLoader('./**/index.md');
console.log('Found pages:', loader.load().length);
"
```

**Common Causes & Solutions:**

#### Missing Required Frontmatter
**Problem:** Section lacks required frontmatter fields
```yaml
# ❌ Missing required fields
---
# No title - section won't render properly
---
```

**Solution:** Add required frontmatter
```yaml
# ✅ Complete frontmatter
---
title: Your Section Title
description: Section description
date: 2024-01-01
---
```

#### Hidden Status
**Problem:** Section marked as hidden
```yaml
---
title: My Section  
hidden: true  # ← This hides the section
---
```

**Solution:** Remove or set to false
```yaml
---
title: My Section
hidden: false  # or remove the line entirely
---
```

#### Incorrect File Location
**Problem:** `index.md` file in wrong location
```bash
# ❌ Wrong location
content/section.md           # Missing directory
content/section/content.md   # Wrong filename

# ✅ Correct location  
content/section/index.md     # Must be named 'index.md'
```

#### Frontmatter Syntax Errors
**Problem:** Invalid YAML syntax
```yaml
# ❌ Syntax errors
---
title: My Section
description: Missing quotes with special chars: & symbols
date 2024-01-01  # Missing colon
---
```

**Solution:** Fix YAML syntax
```yaml
# ✅ Valid syntax
---
title: My Section
description: "Properly quoted text with special chars: & symbols"
date: 2024-01-01
---
```

**Verification Command:**
```bash
# Test YAML syntax
npm install -g js-yaml
js-yaml content/your-section/index.md
```

---

### Issue: Section Appears in Wrong Order

**Symptoms:**
- Sections appear in unexpected order on home page
- New sections appear in wrong position

**Diagnostic Steps:**
```bash
# Check all section dates
grep -r "^date:" content/*/index.md

# See current order
find content -name "index.md" -exec grep -H "date:\|title:" {} \; | sort
```

**Solution:** Fix date ordering
Sections are sorted by date (newest first) in [`theme/pages.js:47-53`](theme/pages.js:47-53).

```yaml
# ✅ To appear first (newest)
date: 2024-12-01

# ✅ To appear second  
date: 2024-06-01

# ✅ To appear third (oldest)
date: 2024-01-01
```

---

## Icon Display Issues

### Issue: Section Has No Icon

**Symptoms:**
- Section tile appears without icon
- Other sections have icons, but new one doesn't

**Diagnostic Steps:**
```bash
# Check current icon mappings
grep -n "i-la-\|i-mdi-\|i-bxs-" theme/components/global/HomeTile.vue

# Check section title exactly
grep "^title:" content/your-section/index.md
```

**Solution:** Add icon mapping
Edit [`theme/components/global/HomeTile.vue:28-36`](theme/components/global/HomeTile.vue:28-36):

```vue
<!-- Add line with exact title match -->
.i-la-your-icon(v-if="item?.frontmatter?.title == 'Your Exact Title'")
```

**Icon Libraries Available:**
- Line Awesome: `i-la-*` ([browse icons](https://icons8.com/line-awesome))
- Material Design: `i-mdi-*` ([browse icons](https://materialdesignicons.com/))
- Boxicons: `i-bxs-*` ([browse icons](https://boxicons.com/))

**Common Icon Names:**
```vue
.i-la-book            # Book/theory
.i-la-hand-point-up   # Practice/interactive
.i-la-tools           # Tools/utilities
.i-la-graduation-cap  # Education
.i-la-users           # Community
.i-la-star            # Featured/premium
.i-la-download        # Downloads
.i-la-external-link-alt # External links
```

---

### Issue: Wrong Icon Appearing

**Symptoms:**
- Section shows incorrect icon
- Icon doesn't match content

**Diagnostic Steps:**
```bash
# Check exact title in frontmatter
grep "title:" content/your-section/index.md

# Check icon mapping
grep -A5 -B5 "Your Title" theme/components/global/HomeTile.vue
```

**Solution:** Fix title matching
Icon matching is case-sensitive and must be exact:

```vue
<!-- ❌ Title mismatch -->
.i-la-book(v-if="item?.frontmatter?.title == 'theory'")     # lowercase
.i-la-book(v-if="item?.frontmatter?.title == 'Theory '")   # extra space

<!-- ✅ Exact match -->  
.i-la-book(v-if="item?.frontmatter?.title == 'Theory'")    # exact match
```

---

## Cover Image Issues

### Issue: Cover Image Not Loading

**Symptoms:**
- Section tile has no background image
- Broken image icon appears
- Console shows 404 errors for images

**Diagnostic Steps:**
```bash
# 1. Check image path in frontmatter
grep "cover:" content/your-section/index.md

# 2. Verify file exists
ls -la content/public/media/your-section/cover.jpg

# 3. Check file permissions
stat content/public/media/your-section/cover.jpg

# 4. Test image URL directly
# Visit: http://localhost:3000/media/your-section/cover.jpg
```

**Common Causes & Solutions:**

#### Incorrect Path Format
```yaml
# ❌ Wrong path formats
cover: media/section/cover.jpg           # Missing leading slash
cover: /content/public/media/cover.jpg   # Incorrect full path
cover: ./media/cover.jpg                 # Relative path won't work

# ✅ Correct format
cover: /media/section/cover.jpg          # Relative to content/public/
```

#### File Doesn't Exist
```bash
# Create missing directory
mkdir -p content/public/media/your-section

# Add image file
cp your-image.jpg content/public/media/your-section/cover.jpg
```

#### Image Processing Issues
Images are processed by [`pages.data.js:10`](content/pages.data.js:10). Check console for processing errors:

```bash
# Restart dev server to reprocess images
npm run dev
```

**Supported Formats:**
- ✅ JPG, JPEG
- ✅ PNG  
- ✅ WebP
- ✅ SVG
- ❌ TIFF, BMP (not web-compatible)

---

### Issue: Cover Image Looks Distorted

**Symptoms:**
- Image appears stretched or pixelated
- Image doesn't fit tile properly

**Solution:** Optimize image dimensions
Images are processed to max 1200×1000px. For best results:

```bash
# Optimal dimensions for tiles
# Width: 1200px (or 800-1600px range)
# Height: 800px (4:3 ratio) or 675px (16:9 ratio)
# Format: JPG with 80-90% quality

# Using ImageMagick to resize
convert input.jpg -resize 1200x800^ -gravity center -extent 1200x800 cover.jpg
```

---

## Color and Layout Issues

### Issue: Section Colors Look Wrong

**Symptoms:**
- Colors don't distribute evenly
- Some sections have similar colors
- Colors change unexpectedly

**Understanding Color System:**
Colors are generated automatically using [`lchToHsl(i, total)`](theme/components/global/HomeTile.vue:38) in [`HomeTile.vue`](theme/components/global/HomeTile.vue).

- `i` = section position (0, 1, 2, ...)  
- `total` = total number of visible sections

**Why Colors Change:**
- Adding/removing sections changes `total`
- Changing section order changes `i` position
- Hidden sections don't count toward `total`

**This is Expected Behavior:**
The system automatically distributes colors evenly. With 3 sections, you get red/green/blue. With 4 sections, you get red/yellow/green/cyan.

**If You Need Consistent Colors:**
Modify [`HomeTile.vue:38`](theme/components/global/HomeTile.vue:38) to use fixed colors:

```vue
<!-- Replace dynamic color with fixed color -->
:style="{ 
  textDecorationColor: getFixedColor(item.frontmatter.title)
}"

<!-- Add method to component -->
<script>
function getFixedColor(title) {
  const colors = {
    'Theory': 'hsl(0, 70%, 50%)',     // Red
    'Practice': 'hsl(120, 70%, 50%)', // Green  
    'Academy': 'hsl(240, 70%, 50%)',  // Blue
    // Add more fixed mappings
  }
  return colors[title] || 'hsl(0, 0%, 50%)'  // Default gray
}
</script>
```

---

### Issue: Home Page Layout Broken

**Symptoms:**
- Tiles appear in single column
- Layout looks cramped or spread out
- Responsive design not working

**Diagnostic Steps:**
```bash
# Check for CSS conflicts
# Open browser dev tools, check for layout errors

# Verify home layout detection
grep -n "layout.*home" content/index.md
grep -n "f.layout.*home" theme/layout.vue
```

**Common Solutions:**

#### Missing Home Layout
```yaml
# ❌ Missing layout declaration
---
title: Site Name
---

# ✅ Correct home layout
---
layout: home
title: Site Name  
---
```

#### CSS Conflicts
Check browser dev tools for CSS conflicts. Common issues:
- Custom CSS overriding flex layout
- Missing responsive breakpoints
- Z-index conflicts

#### Viewport Issues
```html
<!-- Ensure viewport meta tag exists in <head> -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

---

## Build and Development Issues

### Issue: Development Server Not Updating

**Symptoms:**
- Changes to content don't appear
- Hot reload not working
- Stale content displayed

**Solutions:**

#### Restart Development Server
```bash
# Stop server (Ctrl+C)
# Clear cache and restart
rm -rf .vitepress/cache
npm run dev
```

#### Clear Browser Cache
```bash
# Hard refresh in browser
# Chrome/Firefox: Ctrl+Shift+R (or Cmd+Shift+R on Mac)
```

#### Check File Watch Limits (Linux)
```bash
# Increase file watch limit
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

---

### Issue: Build Failing

**Symptoms:**
- `npm run build` fails with errors
- Missing imports or module errors
- Vue component compilation errors

**Diagnostic Steps:**
```bash
# Run build with verbose output
npm run build --verbose

# Check for TypeScript errors
npx vue-tsc --noEmit

# Validate all markdown files  
find content -name "*.md" -exec js-yaml {} \;
```

**Common Build Errors:**

#### Missing Dependencies
```bash
# Install missing packages
npm install

# Check for peer dependency warnings
npm ls
```

#### Invalid Frontmatter
```bash
# Find files with invalid YAML
find content -name "*.md" -exec sh -c '
  echo "Checking: $1"
  js-yaml "$1" >/dev/null || echo "Invalid YAML in $1"
' _ {} \;
```

#### Component Import Errors
Check [`theme/layout.vue`](theme/layout.vue) and [`HomeTile.vue`](theme/components/global/HomeTile.vue) for missing imports:

```vue
<!-- Common missing imports -->
import { lchToHsl } from '#/use/colors'  <!-- Color utility -->
import { data } from '../../../content/pages.data'  <!-- Content data -->
```

---

### Issue: Production Build Different from Development

**Symptoms:**
- Site works in development but not production
- Images missing in production
- Navigation broken in production

**Diagnostic Steps:**
```bash
# Test production build locally
npm run build
npm run preview

# Check for hardcoded localhost URLs
grep -r "localhost" content/

# Verify image paths
find content/public -name "*.jpg" -o -name "*.png" -o -name "*.webp"
```

**Solutions:**

#### Relative Path Issues
```yaml
# ❌ Hardcoded development URLs
iframe: http://localhost:3000/app

# ✅ Relative URLs
iframe: /app
```

#### Case Sensitivity Issues
```bash
# Linux/production is case-sensitive
# Ensure file names match exactly
ls -la content/public/media/Section/Cover.jpg  # Wrong case
ls -la content/public/media/section/cover.jpg  # Correct case
```

---

## Performance Issues

### Issue: Slow Home Page Loading

**Symptoms:**
- Home page takes long to load
- Images loading slowly
- Poor Core Web Vitals scores

**Diagnostic Steps:**
```bash
# Check image sizes
find content/public/media -name "*.jpg" -exec ls -lh {} \;

# Test build size
npm run build
du -sh .vitepress/dist

# Check for large files
find .vitepress/dist -size +1M -exec ls -lh {} \;
```

**Solutions:**

#### Optimize Images
```bash
# Compress images
for img in content/public/media/*/*.jpg; do
  convert "$img" -quality 80 -resize 1200x800^ "$img"
done

# Convert to WebP for better compression
for img in content/public/media/*/*.jpg; do
  cwebp -q 80 "$img" -o "${img%.jpg}.webp"
done
```

#### Enable Image Optimization
Update [`pages.data.js`](content/pages.data.js) to generate multiple sizes:

```javascript
mediaTypes: {
  cover: { 
    size: [800, 1200, 1600],  // Multiple sizes
    height: 1000, 
    fit: "inside",
    format: 'webp'            // Better compression
  }
}
```

---

## Debugging Commands

### General System Health Check
```bash
#!/bin/bash
echo "=== VitePress System Health Check ==="

echo "1. Checking content structure..."
find content -name "index.md" | head -10

echo "2. Checking for required frontmatter..."
for file in content/*/index.md; do
  if ! grep -q "^title:" "$file"; then
    echo "Missing title: $file"
  fi
  if ! grep -q "^date:" "$file"; then
    echo "Missing date: $file"  
  fi
done

echo "3. Checking image files..."
find content/public/media -name "*.jpg" -o -name "*.png" | wc -l
echo "Found images"

echo "4. Checking for hidden sections..."
grep -l "hidden: true" content/*/index.md

echo "5. Testing build..."
npm run build >/dev/null 2>&1
if [ $? -eq 0 ]; then
  echo "Build successful"
else
  echo "Build failed - check npm run build for details"
fi
```

### Content Validation Script
```bash
#!/bin/bash
echo "=== Content Validation ==="

for file in content/*/index.md; do
  echo "Checking: $file"
  
  # Validate YAML
  if ! js-yaml "$file" >/dev/null 2>&1; then
    echo "  ❌ Invalid YAML syntax"
  fi
  
  # Check required fields
  if ! grep -q "^title:" "$file"; then
    echo "  ❌ Missing title"
  fi
  
  if ! grep -q "^description:" "$file"; then
    echo "  ⚠️  Missing description (recommended)"
  fi
  
  # Check cover image exists
  cover=$(grep "^cover:" "$file" | cut -d: -f2- | tr -d ' ')
  if [ -n "$cover" ] && [ ! -f "content/public$cover" ]; then
    echo "  ❌ Cover image not found: content/public$cover"
  fi
  
  echo "  ✅ Valid"
done
```

### Performance Analysis
```bash
#!/bin/bash
echo "=== Performance Analysis ==="

echo "Image sizes:"
find content/public/media -name "*.jpg" -o -name "*.png" | \
  xargs ls -lh | sort -k5 -hr | head -10

echo "Largest files in build:"
npm run build >/dev/null 2>&1
find .vitepress/dist -type f -exec ls -lh {} \; | \
  sort -k5 -hr | head -10
```

## Getting Help

### When to Ask for Support
If you've tried the solutions above and still have issues:

1. **Gather diagnostic information:**
   ```bash
   # System info
   node --version
   npm --version
   
   # Project info
   npm run build 2>&1 | head -20
   
   # Content structure
   find content -name "index.md" | head -5
   ```

2. **Document the issue:**
   - What you expected to happen
   - What actually happened  
   - Steps you've already tried
   - Error messages (full text)

3. **Create minimal reproduction:**
   - Simplify to smallest possible example
   - Remove unrelated content/customizations

### Common Error Patterns

**YAML Parsing Errors:**
```
Error: YAMLException: bad indentation of a mapping entry
```
**Fix:** Check frontmatter indentation and syntax

**Module Not Found:**
```
Error: Cannot find module './pages.data'
```
**Fix:** Check file paths and ensure all referenced files exist

**Build Timeout:**
```
Error: Build timeout after 10 minutes
```
**Fix:** Usually indicates infinite loop or very large images - check image sizes

**Navigation Errors:**  
```
Error: Cannot read property 'url' of undefined
```
**Fix:** Usually missing or malformed frontmatter in content files

For additional help beyond this guide, refer to:
- [VitePress Documentation](https://vitepress.dev/)
- [Vue.js Documentation](https://vuejs.org/)
- [System Overview](./system-overview.md) for architecture understanding