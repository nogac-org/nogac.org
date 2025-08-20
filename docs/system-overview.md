# System Overview: How VitePress + Custom Theme Works

This document explains the architecture behind the dynamic home page system, designed for developers new to Vue.js/VitePress who need to understand how all the pieces fit together.

## VitePress Foundation

### What is VitePress?
VitePress is a static site generator built on Vue.js that:
- Compiles Markdown files into static HTML pages
- Uses Vue components for dynamic functionality
- Supports custom themes for complete layout control
- Provides a plugin system for extending functionality

### How This Project Extends VitePress

This project uses a **custom theme** that completely overrides VitePress's default behavior to create a dynamic, tile-based home page system.

## The Home Page Detection System

### 1. Layout Triggering

The magic starts in [`content/index.md:2`](content/index.md:2):

```yaml
---
layout: home
---
```

This `layout: home` frontmatter tells VitePress to use special rendering logic.

### 2. Layout Detection Logic

In [`theme/layout.vue:60`](theme/layout.vue:60), the system checks for this layout:

```vue
template(v-else-if="f.layout == 'home'")
  main.home.items-center.justify-center.overflow-clip
    <!-- Home page specific content -->
```

When `layout: home` is detected, VitePress renders the home layout instead of the standard page layout.

## Content Discovery Pipeline

### 1. Automatic Page Discovery

The [`content/pages.data.js`](content/pages.data.js) file uses VitePress's `createContentLoader` to automatically find all index pages:

```javascript
// Line 5: Glob pattern finds all index.md files
const pages = createContentLoader('./**/index.md', {
  includeSrc: true,
  transform: VPMedia({
    root: new URL('./', import.meta.url),
    mediaTypes: {
      cover: { size: 1200, height: 1000, fit: "inside" }
    }
  })
})
```

**What this does:**
- Scans the entire `content/` directory
- Finds every `index.md` file in any subdirectory
- Processes cover images automatically
- Makes this data available to Vue components

### 2. Page Organization

The [`theme/pages.js`](theme/pages.js) file contains utilities for organizing discovered pages:

```javascript
// Line 42: Filter out hidden pages
if (route.frontmatter?.hidden || route.url == '/') continue
```

**Key functions:**
- `getPages()` - Groups pages by their parent directory
- `getParents()` - Builds breadcrumb navigation
- `getSiblings()` - Enables next/previous navigation
- Hidden page filtering - Respects `hidden: true` frontmatter

### 3. Data Flow to Components

The data flows like this:
```
pages.data.js → theme/pages.js → layout.vue → HomeTile.vue
```

## Home Page Rendering Pipeline

### 1. Section Discovery

In [`theme/layout.vue:77-81`](theme/layout.vue:77-81), the home layout iterates through discovered sections:

```vue
home-tile(
  style="flex: 1 1 280px;"
  v-for="(area, i) in children", 
  :key="area.url", 
  :item="area", 
  :i="i",
  :total="children.length")
```

### 2. Individual Tile Rendering

Each section becomes a [`HomeTile.vue`](theme/components/global/HomeTile.vue) component with:

- **Dynamic colors**: Generated using [`lchToHsl(i, total)`](theme/components/global/HomeTile.vue:38)
- **Custom icons**: Hardcoded based on section title in [`HomeTile.vue:28-36`](theme/components/global/HomeTile.vue:28-36)
- **Background images**: From frontmatter `cover` property
- **Child pages**: Automatically discovered and displayed as sub-navigation

### 3. Icon Assignment Logic

Icons are assigned based on exact title matching:

```vue
.i-la-book(v-if="item?.frontmatter?.title == 'Theory'")
.i-la-hand-point-up(v-if="item?.frontmatter?.title == 'Practice'")
.i-la-chalkboard-teacher(v-if="item?.frontmatter?.title == 'Academy'")
<!-- ... more icon mappings -->
```

**Important:** New sections won't have icons unless you add mappings here.

## Architecture Benefits

### 1. Zero Configuration Content Management
- Drop a directory with `index.md` → appears on home page
- Add `hidden: true` → disappears from home page
- No manual registration or configuration needed

### 2. Automatic Child Discovery
- Any `index.md` files in subdirectories automatically appear as sub-navigation
- Maintains hierarchical relationships without manual setup

### 3. Dynamic Visual Organization
- Colors are mathematically distributed using [`lchToHsl()`](theme/components/global/HomeTile.vue:38)
- Layout adapts to any number of sections
- Responsive design built-in

### 4. VitePress Integration
- All standard VitePress features still work (routing, markdown processing, etc.)
- Custom theme only affects the home page layout
- Individual pages use standard VitePress rendering

## Key Architectural Decisions

### Why Custom Theme Instead of Plugins?
- **Complete control**: Can override any VitePress behavior
- **Integrated styling**: Theme and functionality are tightly coupled
- **Performance**: No plugin overhead, direct Vue rendering

### Why Glob Pattern Discovery?
- **Automatic**: No manual registration of new sections
- **Convention over configuration**: Follow the pattern, get the behavior
- **Maintainable**: Adding content doesn't require code changes

### Why Icon Hardcoding?
- **Predictable**: Icons don't change unexpectedly
- **Performant**: No dynamic icon loading
- **Simple**: Easy to understand and modify

## Data Flow Summary

1. **Build Time**: [`pages.data.js:5`](content/pages.data.js:5) discovers all `index.md` files
2. **Runtime**: [`layout.vue:60`](theme/layout.vue:60) detects `layout: home`
3. **Organization**: [`pages.js:42`](theme/pages.js:42) filters and organizes pages
4. **Rendering**: [`HomeTile.vue`](theme/components/global/HomeTile.vue) renders each section
5. **Styling**: [`lchToHsl()`](theme/components/global/HomeTile.vue:38) provides dynamic colors

This architecture creates a maintainable, scalable system where content creators can focus on writing Markdown while the system automatically handles presentation and organization.

## Next Steps

- **Adding Content**: See [Content Management](./content-management.md)
- **Understanding Files**: See [File Structure](./file-structure.md)
- **Configuration Options**: See [Configuration Reference](./configuration-reference.md)