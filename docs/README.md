# VitePress Index Page System Documentation

Welcome to the comprehensive documentation for the VitePress-based index page system used in this project. This documentation is designed for developers new to Vue.js/VitePress who need to understand, maintain, and modify the dynamic home page system.

## Quick Navigation

| Document | Purpose | Best For |
|----------|---------|----------|
| **[System Overview](./system-overview.md)** | Understand how VitePress + custom theme works together | Getting the big picture |
| **[Content Management](./content-management.md)** | Step-by-step guide to add/remove sections | Adding new content areas |
| **[Configuration Reference](./configuration-reference.md)** | Complete frontmatter properties guide | Setting up page metadata |
| **[File Structure](./file-structure.md)** | Key files and their roles in the system | Understanding the codebase |
| **[Examples](./examples.md)** | Real-world examples and copy-paste templates | Quick implementation |
| **[Troubleshooting](./troubleshooting.md)** | Common issues and diagnostic solutions | Fixing problems |

## System At-a-Glance

This VitePress site uses a dynamic home page system that automatically discovers and displays content sections:

- **Automatic Discovery**: All [`index.md`](content/index.md) files are automatically found using glob pattern [`./**/index.md`](content/pages.data.js:5)
- **Dynamic Layout**: The [`layout: home`](content/index.md:2) frontmatter triggers special home page rendering in [`theme/layout.vue:60`](theme/layout.vue:60)
- **Smart Filtering**: Pages with [`hidden: true`](theme/pages.js:42) frontmatter are automatically excluded
- **Visual Tiles**: Each section is rendered as an interactive tile by [`HomeTile.vue`](theme/components/global/HomeTile.vue)
- **Color Coding**: Dynamic colors are generated using [`lchToHsl(i, total)`](theme/components/global/HomeTile.vue:38) for visual distinction

## Quick Start

### Adding a New Section
1. Create directory: `content/my-new-section/`
2. Add file: `content/my-new-section/index.md`
3. Include frontmatter:
   ```yaml
   ---
   title: My New Section
   description: Brief description of this section
   cover: /path/to/cover-image.jpg
   date: 2024-01-01
   ---
   ```
4. Section appears automatically on home page

### Hiding a Section
Add `hidden: true` to the frontmatter:
```yaml
---
title: My Section
hidden: true
---
```

## Key Files Overview

- [`content/index.md`](content/index.md) - Main home page with `layout: home`
- [`theme/layout.vue`](theme/layout.vue) - Layout logic and home page detection
- [`content/pages.data.js`](content/pages.data.js) - Content discovery system
- [`theme/components/global/HomeTile.vue`](theme/components/global/HomeTile.vue) - Section tile rendering
- [`theme/pages.js`](theme/pages.js) - Page organization utilities

## Need Help?

1. **New to the system?** Start with [System Overview](./system-overview.md)
2. **Want to add content?** See [Content Management](./content-management.md)
3. **Having issues?** Check [Troubleshooting](./troubleshooting.md)
4. **Need examples?** Browse [Examples](./examples.md)

---

*This documentation covers the VitePress index page system as implemented in this project. All file references include line numbers for easy navigation.*