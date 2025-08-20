# File Structure: Key Files and Their Roles

This guide explains the critical files in the VitePress index page system, their specific roles, and how they interact to create the dynamic home page functionality.

## Core System Files

### 1. [`content/index.md`](content/index.md)
**Role:** Main home page definition  
**Key Lines:** 
- Line 2: `layout: home` - Triggers home page rendering
- Line 3-6: Site metadata and YouTube embed configuration

```yaml
---
layout: home                    # Line 2: Activates custom home layout
title: Musetta Stone           # Site title
description: Visual Music Language Research Hub
youtube: qthKClCRIl0          # YouTube video embed
date: 2017-01-01
---
```

**What it does:**
- Defines the main home page accessible at `/`
- Contains site-wide branding and introduction content
- Triggers the special home layout that displays section tiles

**When to modify:**
- Changing site title or description
- Adding/removing YouTube video embed
- Updating home page content

---

### 2. [`content/pages.data.js`](content/pages.data.js) 
**Role:** Content discovery engine  
**Key Lines:**
- Line 5: `./**/index.md` - Glob pattern for automatic page discovery
- Lines 7-12: Media processing configuration for cover images

```javascript
// Line 5: Core discovery pattern
const pages = createContentLoader('./**/index.md', {
  includeSrc: true,
  transform: VPMedia({
    root: new URL('./', import.meta.url),
    mediaTypes: {
      cover: { size: 1200, height: 1000, fit: "inside" } // Line 10: Image processing
    }
  })
})
```

**What it does:**
- Automatically finds all `index.md` files in the content directory
- Processes cover images to optimized sizes
- Provides data to Vue components for rendering
- Runs at build time to create the page database

**When to modify:**
- Changing cover image processing settings
- Adding new media types
- Modifying discovery patterns

---

### 3. [`theme/layout.vue`](theme/layout.vue)
**Role:** Main layout controller and home page renderer  
**Critical Sections:**

#### Lines 60-86: Home Layout Detection and Rendering
```vue
template(v-else-if="f.layout == 'home'")           # Line 60: Layout detection
  main.home.items-center.justify-center.overflow-clip
    chroma-flower.flex.justify-center
    .flex-1.p-8.gap-1.flex.flex-col.origin-left
      .text-3rem.md-text-4rem.font-bold Musetta Stone
      .text-2rem.md-ml-1 More Than Music...
    .flex.flex-wrap.items-start.px-4.gap-8
      .flex.flex-wrap.gap-16.items-stretch
        home-tile(                                # Lines 75-81: Section tile rendering
          style="flex: 1 1 280px;"
          v-for="(area, i) in children", 
          :key="area.url", 
          :item="area", 
          :i="i",
          :total="children.length")
```

**What it does:**
- Detects when `layout: home` is set in frontmatter
- Renders site branding and introductory content
- Iterates through discovered sections to create tiles
- Manages overall page layout and responsiveness

**When to modify:**
- Changing home page structure
- Adding new layout types
- Modifying tile grid behavior

---

### 4. [`theme/components/global/HomeTile.vue`](theme/components/global/HomeTile.vue)
**Role:** Individual section tile renderer  
**Critical Sections:**

#### Lines 28-36: Icon Assignment Logic
```vue
.text-3xl.mt-1(style="flex: 0 1 30px") 
  .i-la-book(v-if="item?.frontmatter?.title == 'Theory'")
  .i-la-hand-point-up(v-if="item?.frontmatter?.title == 'Practice'")
  .i-la-chalkboard-teacher(v-if="item?.frontmatter?.title == 'Academy'")
  .i-la-shopping-bag(v-if="item?.frontmatter?.title == 'Shop'")
  .i-la-star(v-if="item?.frontmatter?.title == 'Support'")
  .i-la-at(v-if="item?.frontmatter?.title == 'Contacts'")
```

#### Lines 38-39: Dynamic Color Assignment
```vue
:style="{ textDecorationColor: lchToHsl(i, total) }"  # Line 38
```

#### Lines 41-47: Child Page Sub-Navigation
```vue
.flex.flex-wrap.py-3.gap-2(v-if="children?.length > 0")
  a.cursor-pointer.backdrop-blur.shadow-sm.rounded-lg
    v-for="(page, p) in children" :key="page.url"
```

**What it does:**
- Renders each section as a visual tile
- Assigns icons based on exact title matching
- Generates dynamic colors using mathematical distribution
- Displays child pages as sub-navigation links
- Handles cover image backgrounds with gradient overlays

**When to modify:**
- Adding icons for new sections
- Changing tile visual design
- Modifying color generation

---

### 5. [`theme/pages.js`](theme/pages.js)
**Role:** Page organization and utilities  
**Critical Functions:**

#### Lines 38-56: Page Organization Logic
```javascript
export function getPages(routes) {
  let pageList = {}
  for (let route of routes) {
    const folder = normalize(route.url.split("/").slice(0, -2).join("/"))
    if (route.frontmatter?.hidden || route.url == '/') continue  // Line 42: Hidden filtering
    pageList[folder] = pageList[folder] || [];
    pageList[folder].push(route);
  }
  // Lines 47-53: Date-based sorting
  for (let folder in pageList) {
    pageList[folder].sort((a, b) => {
      if (a.frontmatter?.date && b.frontmatter?.date) {
        return a.frontmatter.date > b.frontmatter.date ? -1 : 1;
      }
    });
  }
  return pageList;
}
```

#### Lines 97-103: URL Utilities
```javascript
export function normalize(url) {
  return (url += url.endsWith("/") ? "" : "/");        // Line 98
}

export function cleanLink(url) {
  return (url || '').replace(/\/[^/]*\.(html)$/, '/')  // Line 102
}
```

**What it does:**
- Groups pages by their parent directories
- Filters out hidden pages (`hidden: true` in frontmatter)
- Sorts sections by date (newest first)
- Provides URL normalization utilities
- Builds navigation hierarchies

**When to modify:**
- Changing sort order logic
- Adding new filtering criteria
- Modifying URL handling

## Supporting Files

### [`theme/composables/draw.js`](theme/composables/draw.js)
**Role:** Drawing overlay functionality (referenced in layout.vue:7)  
**Usage:** Enables interactive drawing features on pages

### [`theme/media.js`](theme/media.js)
**Role:** Media processing utilities (referenced in pages.data.js:2)  
**Usage:** Handles image optimization and transformation

### [`components/chroma/`](components/chroma/)
**Role:** Custom Vue components for visual elements  
**Usage:** Color-based interactive components used throughout the site

## Directory Structure Overview

```
project-root/
├── content/                    # All site content
│   ├── index.md               # Main home page (layout: home)
│   ├── pages.data.js          # Content discovery system
│   ├── section-1/
│   │   ├── index.md          # Section page (appears as tile)
│   │   └── subsection/
│   │       └── index.md      # Child page (appears in sub-nav)
│   └── section-2/
│       └── index.md
├── theme/                      # Custom VitePress theme
│   ├── layout.vue             # Main layout controller
│   ├── pages.js               # Page organization utilities
│   └── components/
│       └── global/
│           └── HomeTile.vue   # Section tile renderer
├── components/                 # Reusable Vue components
└── docs/                      # This documentation
```

## File Interaction Flow

### 1. Build Time Discovery
```
pages.data.js (glob discovery) → finds all index.md files → builds page database
```

### 2. Page Rendering Request
```
User visits / → layout.vue checks frontmatter → detects layout: home
```

### 3. Home Page Rendering
```
layout.vue → uses pages.js utilities → organizes sections → renders HomeTile.vue components
```

### 4. Tile Generation
```
HomeTile.vue → applies icons → generates colors → displays content → shows child navigation
```

## File Dependencies

### Critical Dependencies
- **layout.vue** depends on **pages.data.js** for section data
- **HomeTile.vue** depends on **pages.js** for child page discovery
- **pages.data.js** depends on **media.js** for image processing
- All components depend on **index.md** files having proper frontmatter

### Breaking Changes Impact
- **Modifying pages.data.js glob pattern**: Affects section discovery
- **Changing layout.vue home detection**: Breaks home page rendering
- **Modifying HomeTile.vue structure**: Affects all section tiles
- **Breaking pages.js utilities**: Affects navigation throughout site

## File Modification Guidelines

### Safe Modifications
- **Adding frontmatter properties**: Extend configuration without breaking existing
- **Updating styles**: Visual changes don't affect functionality
- **Adding new icon mappings**: Extends functionality for new sections

### High-Risk Modifications
- **Changing glob patterns in pages.data.js**: May break content discovery
- **Modifying layout detection in layout.vue**: Could break home page
- **Changing URL utilities in pages.js**: May break navigation

### Required Updates When...

#### Adding New Layout Types
1. Update **layout.vue** template detection
2. Add corresponding rendering logic
3. Update this documentation

#### Adding New Frontmatter Properties
1. Update **pages.js** if filtering/sorting is needed
2. Update **HomeTile.vue** if display is needed
3. Update **configuration-reference.md** documentation

#### Changing Section Discovery
1. Update **pages.data.js** glob pattern
2. Test with existing content structure
3. Update **content-management.md** documentation

## Debugging File Issues

### Check File Loading
```bash
# Verify pages.data.js finds your content
node -e "console.log(require('./content/pages.data.js'))"

# Check for syntax errors in Vue files
npm run build
```

### Verify File Relationships
```bash
# Find all references to specific files
grep -r "pages.data" theme/
grep -r "HomeTile" theme/
```

### Test File Modifications
1. **Make small changes**: Test one file at a time
2. **Check browser console**: Look for Vue component errors
3. **Verify hot reload**: Changes should appear without full restart
4. **Test production build**: `npm run build` to verify build compatibility

## Performance Considerations

### File Size Impact
- **pages.data.js**: Processed at build time, doesn't affect runtime performance
- **layout.vue**: Loaded on every page, keep efficient
- **HomeTile.vue**: Instantiated once per section, optimize rendering
- **pages.js**: Utilities called frequently, keep functions lightweight

### Build Time Impact
- **Content discovery**: More index.md files = longer build times
- **Image processing**: Cover images processed during build
- **Component compilation**: Complex Vue components slow down builds

For troubleshooting file-related issues, see [Troubleshooting Guide](./troubleshooting.md).

## Next Steps

- **See Real Examples**: [Examples](./examples.md)
- **Understand Configuration**: [Configuration Reference](./configuration-reference.md)
- **Content Management**: [Content Management](./content-management.md)