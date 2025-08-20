# Configuration Reference: Complete Frontmatter Properties Guide

This guide documents all frontmatter properties supported by the VitePress index page system. Frontmatter appears at the top of Markdown files between `---` delimiters and controls page behavior and display.

## Standard Properties

### `title` (Required)
**Type:** `string`  
**Purpose:** Display name for the section or page  
**Used in:** Section tiles, navigation, page headers  

```yaml
---
title: Music Theory
---
```

**Implementation:** Referenced in [`HomeTile.vue:37-39`](theme/components/global/HomeTile.vue:37-39) for tile display.

**Best Practices:**
- Keep under 20 characters for tile display
- Use title case: "Music Theory" not "music theory"
- Be descriptive and unique

---

### `description` (Recommended)
**Type:** `string`  
**Purpose:** Brief summary shown in tiles and metadata  
**Used in:** Home page tiles, SEO meta tags, navigation hints

```yaml
---
title: Music Theory
description: Explore the fundamental concepts of harmony, melody, and rhythm
---
```

**Implementation:** Displayed in [`HomeTile.vue:40`](theme/components/global/HomeTile.vue:40).

**Best Practices:**
- 50-100 characters optimal for tiles
- Complete sentences preferred
- Avoid duplicating the title

---

### `date` (Required for Ordering)
**Type:** `string` (ISO date format)  
**Purpose:** Controls section ordering on home page  
**Used in:** Section sorting logic

```yaml
---
title: Music Theory
date: 2024-01-01
---
```

**Implementation:** Sorting handled in [`theme/pages.js:47-53`](theme/pages.js:47-53) - newer dates appear first.

**Formats:**
- Full date: `2024-01-01`
- With time: `2024-01-01T10:30:00`
- Year-month: `2024-01`

**Ordering Logic:**
```yaml
# This appears first (newest)
date: 2024-12-01

# This appears second  
date: 2024-06-01

# This appears last (oldest)
date: 2024-01-01
```

---

### `cover` (Recommended)
**Type:** `string` (file path)  
**Purpose:** Background image for section tiles  
**Used in:** Home page tile backgrounds

```yaml
---
title: Music Theory
cover: /media/theory/cover.jpg
---
```

**Implementation:** Applied as background in [`HomeTile.vue:20`](theme/components/global/HomeTile.vue:20).

**Path Requirements:**
- Relative to `content/public/`
- Example: `cover: /media/section/image.jpg` → `content/public/media/section/image.jpg`

**Image Specifications:**
- **Processed size**: Max 1200px × 1000px ([`pages.data.js:10`](content/pages.data.js:10))
- **Aspect ratio**: 16:9 or 4:3 recommended for tiles
- **Formats**: JPG, PNG, WebP, SVG
- **Optimization**: Compress for web performance

**Visual Effect:**
Images are overlaid with gradient for text readability:
```css
background-image: linear-gradient(hsla(0,0%,100%,0.3), hsla(0,0%,50%,0.5)), url(cover-image)
```

---

## Special Properties

### `layout`
**Type:** `string`  
**Purpose:** Triggers special page rendering modes  
**Values:** `home`, `iframe`

#### `layout: home`
**Used in:** [`content/index.md:2`](content/index.md:2)  
**Effect:** Triggers home page rendering with section tiles

```yaml
---
layout: home
title: Site Name
---
```

**Implementation:** Detected in [`theme/layout.vue:60`](theme/layout.vue:60) to render tile-based home layout.

#### `layout: iframe`
**Effect:** Embeds external content in full-page iframe

```yaml
---
layout: iframe
iframe: https://example.com/app
title: External App
---
```

**Implementation:** Handled in [`theme/layout.vue:53-58`](theme/layout.vue:53-58).

---

### `hidden`
**Type:** `boolean`  
**Default:** `false`  
**Purpose:** Hide sections from home page display  
**Used in:** Content filtering

```yaml
---
title: Draft Section
hidden: true
---
```

**Implementation:** Filtered out in [`theme/pages.js:42`](theme/pages.js:42).

**Use Cases:**
- Work-in-progress content
- Seasonal sections (temporarily disabled)
- A/B testing alternate content
- Archive sections

**Restoration:**
Remove `hidden: true` line to restore section visibility.

---

### `youtube`
**Type:** `string` (YouTube video ID)  
**Purpose:** Embed YouTube video on home page  
**Used in:** Main home page content area

```yaml
---
layout: home
youtube: qthKClCRIl0
---
```

**Implementation:** Rendered in [`theme/layout.vue:83-84`](theme/layout.vue:83-84).

**Video ID Extraction:**
- From URL: `https://youtube.com/watch?v=qthKClCRIl0` → `qthKClCRIl0`
- From short URL: `https://youtu.be/qthKClCRIl0` → `qthKClCRIl0`

---

### `iframe`
**Type:** `string` (URL)  
**Purpose:** Embed external content in iframe  
**Used in:** Full-page content embedding

```yaml
---
title: External Tool
iframe: https://example.com/tool
layout: iframe
---
```

**Implementation:** 
- Detection: [`theme/layout.vue:55`](theme/layout.vue:55)
- Rendering: [`theme/layout.vue:105-108`](theme/layout.vue:105-108)

**Iframe Permissions:**
Default allowed features: `midi;microphone;fullscreen;`

**Security Considerations:**
- Only embed trusted sources
- Check CORS policies
- Verify HTTPS for secure contexts

---

## Advanced Properties

### `topContent`
**Type:** `boolean`  
**Purpose:** Controls content placement relative to child navigation  
**Default:** Content appears after child navigation

```yaml
---
title: My Section
topContent: true
---

# This content appears BEFORE child page navigation
```

**Implementation:** Conditional rendering in [`theme/layout.vue:113`](theme/layout.vue:113) and [`theme/layout.vue:117`](theme/layout.vue:117).

---

### `dynamic`
**Type:** `boolean`  
**Purpose:** Special handling for dynamic page content  
**Used in:** Advanced content management scenarios

```yaml
---
title: Dynamic Section
dynamic: true
---
```

**Implementation:** Affects parent navigation in [`theme/layout.vue:94`](theme/layout.vue:94).

## Property Combinations

### Standard Section
```yaml
---
title: Music Theory
description: Explore fundamental concepts of music
cover: /media/theory/cover.jpg
date: 2024-01-01
---
```

### Hidden Section
```yaml
---
title: Work in Progress
description: Content under development
cover: /media/wip/cover.jpg  
date: 2024-01-01
hidden: true
---
```

### Home Page
```yaml
---
layout: home
title: Chromatone
description: Visual Music Language
youtube: qthKClCRIl0
date: 2024-01-01
---
```

### Iframe Page
```yaml
---
layout: iframe
iframe: https://app.example.com
title: External Application
description: Interactive music tool
---
```

## Property Validation

### Required Combinations
- **All sections**: Must have `title`
- **Home page tiles**: Need `title`, `description`, `date`
- **Visual appeal**: Should have `cover` image
- **Ordering**: Must have `date` for predictable positioning

### Common Mistakes
```yaml
# ❌ Missing required properties
---
title: My Section
# Missing date - unpredictable ordering
# Missing description - empty tile text
---

# ✅ Complete configuration
---
title: My Section
description: Clear, concise description
cover: /media/section/cover.jpg
date: 2024-01-01
---
```

### Property Conflicts
```yaml
# ❌ Conflicting layouts
---
layout: home
iframe: https://example.com  # iframe ignored in home layout
---

# ✅ Correct iframe usage
---
layout: iframe
iframe: https://example.com
---
```

## Property Inheritance

### Child Pages
Child pages inherit some behavior from parent sections:
- Color theming (based on parent position)
- Navigation hierarchy
- Section context

### Global Defaults
Some properties have system defaults:
- `hidden: false` (sections visible by default)
- `layout: default` (standard page rendering)
- `topContent: false` (content after navigation)

## Custom Properties

### Adding New Properties
To add custom properties:

1. **Update theme components** to recognize new properties
2. **Document usage** in this reference
3. **Add validation** if required

Example addition:
```vue
<!-- In HomeTile.vue -->
<div v-if="item?.frontmatter?.priority" class="priority-badge">
  {{ item.frontmatter.priority }}
</div>
```

### Reserved Properties
Avoid these property names (used by VitePress core):
- `head`
- `lang`
- `titleTemplate`
- `outline`
- `aside`
- `footer`

## Debugging Properties

### Check Property Usage
```bash
# Find all files using specific property
grep -r "^cover:" content/*/index.md

# Find sections without descriptions
grep -L "^description:" content/*/index.md

# Find hidden sections
grep -r "^hidden: true" content/*/index.md
```

### Validate Frontmatter
Use tools like `js-yaml` to validate YAML syntax:
```bash
npm install -g js-yaml
js-yaml content/section/index.md
```

## Performance Considerations

### Image Properties
- **Large covers**: Automatically resized by [`pages.data.js:10`](content/pages.data.js:10)
- **Format optimization**: WebP preferred for best performance
- **Loading**: Images are lazy-loaded on home page

### Content Properties
- **Description length**: Longer descriptions may cause layout issues
- **Title length**: Very long titles may overflow tiles
- **Date parsing**: Invalid dates fall back to file modification time

## Migration Guide

### From Manual Configuration
If migrating from a system with manual section registration:

1. **Add frontmatter** to all `index.md` files
2. **Set dates** for desired ordering  
3. **Add descriptions** for home page display
4. **Remove manual registration** code
5. **Test section discovery** with glob pattern

### Property Renaming
If renaming properties:
1. **Update all content files** with new property names
2. **Update theme components** to use new properties
3. **Update this documentation** with new reference
4. **Test thoroughly** before deploying

For troubleshooting property issues, see [Troubleshooting Guide](./troubleshooting.md).

## Next Steps

- **See Real Examples**: [Examples](./examples.md)
- **Understand File Structure**: [File Structure](./file-structure.md) 
- **Content Management**: [Content Management](./content-management.md)