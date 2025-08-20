# Content Management: Step-by-Step Section Addition and Removal

This guide provides practical instructions for adding, removing, and managing content sections in the VitePress home page system. All changes are automatically detected - no manual registration required.

## Understanding the Section System

### What Makes a Section
A "section" is any directory containing an `index.md` file that will appear as a tile on the home page.

**Directory structure:**
```
content/
├── my-section/           # Section directory
│   └── index.md         # Required: Makes it a section
├── my-section/          
│   ├── index.md         # Main section page
│   └── subsection/      
│       └── index.md     # Child page (appears in sub-navigation)
```

### Automatic Discovery
The system uses [`content/pages.data.js:5`](content/pages.data.js:5) to find all `index.md` files:
- Scans with pattern `./**/index.md`
- Discovers sections at build time
- No manual configuration needed

## Adding a New Section

### Step 1: Create the Directory Structure
```bash
# Create directory in content folder
mkdir content/my-new-section
```

### Step 2: Create the Main Index File
Create `content/my-new-section/index.md`:

```markdown
---
title: My New Section
description: Brief description that appears on the home page tile
cover: /media/my-section-cover.jpg
date: 2024-01-01
---

# My New Section

Your section content goes here. This page will be displayed when users click the tile.

## Features
- Feature 1
- Feature 2
- Feature 3
```

### Step 3: Add an Icon (Optional)
To add a custom icon, edit [`theme/components/global/HomeTile.vue:28-36`](theme/components/global/HomeTile.vue:28-36):

```vue
.i-la-star(v-if="item?.frontmatter?.title == 'My New Section'")
```

**Available icon libraries:**
- `i-la-*` - Line Awesome icons
- `i-mdi-*` - Material Design icons
- `i-bxs-*` - Boxicons solid

### Step 4: Verify Section Appearance
1. Restart development server: `npm run dev`
2. Visit home page - your section should appear automatically
3. Check that cover image displays correctly
4. Test clicking the tile to navigate to the section

## Adding Child Pages (Sub-navigation)

Child pages automatically appear as links within the section tile.

### Create Child Page Structure
```bash
# Add subdirectory with index.md
mkdir content/my-new-section/child-topic
```

### Create Child Index File
Create `content/my-new-section/child-topic/index.md`:

```markdown
---
title: Child Topic
description: Description for child page
date: 2024-01-02
---

# Child Topic Content
```

### Result
The child page will automatically appear in the section tile's sub-navigation area as handled by [`HomeTile.vue:41-47`](theme/components/global/HomeTile.vue:41-47).

## Removing or Hiding Sections

### Method 1: Hide with Frontmatter (Recommended)
Add `hidden: true` to the section's `index.md` frontmatter:

```yaml
---
title: My Section
description: This section is now hidden
hidden: true
---
```

**Benefits:**
- Section is filtered out by [`theme/pages.js:42`](theme/pages.js:42)
- Content is preserved
- Can be easily restored by removing `hidden: true`

### Method 2: Delete the Directory
```bash
# Permanently removes the section
rm -rf content/my-section
```

**Warning:** This permanently deletes all content. Use Method 1 for temporary hiding.

## Reordering Sections

Sections are ordered by the `date` field in frontmatter, as handled by [`theme/pages.js:47-53`](theme/pages.js:47-53):

```javascript
pageList[folder].sort((a, b) => {
  if (a.frontmatter?.date && b.frontmatter?.date) {
    return a.frontmatter.date > b.frontmatter.date ? -1 : 1;
  } else {
    return 0;
  }
});
```

### To Change Order
Update the `date` values in frontmatter:

```yaml
# This will appear first (newest date)
date: 2024-12-01

# This will appear second
date: 2024-06-01

# This will appear third
date: 2024-01-01
```

## Managing Cover Images

### Image Requirements
- **Size**: Processed to max 1200px width, 1000px height ([`pages.data.js:10`](content/pages.data.js:10))
- **Format**: Any web format (JPG, PNG, WebP, SVG)
- **Location**: Usually in `/content/public/media/` directory

### Setting Cover Images
In the section's `index.md` frontmatter:

```yaml
---
title: My Section
cover: /media/my-section/cover.jpg
---
```

### Background Image Display
Cover images are displayed as backgrounds in [`HomeTile.vue:18-21`](theme/components/global/HomeTile.vue:18-21):

```vue
:style="{ backgroundImage: `linear-gradient(hsla(0,0%,100%,0.3), hsla(0,0%,50%,0.5)), url(${props.item?.frontmatter?.cover})` }"
```

The gradient overlay ensures text readability.

## Section Color Management

### Automatic Color Assignment
Colors are automatically assigned using [`lchToHsl(i, total)`](theme/components/global/HomeTile.vue:38) where:
- `i` = section position (0, 1, 2, ...)
- `total` = total number of sections

### Color Distribution
The system distributes colors evenly around the color wheel:
- 3 sections: Red, Green, Blue
- 4 sections: Red, Yellow, Green, Cyan
- 6 sections: Red, Orange, Yellow, Green, Blue, Purple

Colors automatically adjust when sections are added or removed.

## Best Practices

### Directory Naming
- Use kebab-case: `my-new-section`
- Be descriptive: `music-theory` instead of `theory`
- Avoid special characters

### Frontmatter Guidelines
```yaml
---
title: Clear, Descriptive Title          # Required: Shows on tile
description: One-sentence summary        # Required: Shows on tile  
cover: /media/section/cover.jpg         # Recommended: Visual appeal
date: 2024-01-01                        # Required: Controls ordering
hidden: false                           # Optional: Default is false
---
```

### Content Organization
```
content/
├── main-topic/
│   ├── index.md              # Main section page
│   ├── subtopic-1/
│   │   └── index.md         # Child page
│   ├── subtopic-2/
│   │   └── index.md         # Child page
│   └── advanced/
│       ├── index.md         # Child page
│       └── deep-dive/
│           └── index.md     # Grandchild (won't show in tile)
```

### Performance Considerations
- **Cover images**: Optimize for web (compress, appropriate dimensions)
- **Child pages**: Limit to 5-8 for visual clarity in tiles
- **Deep nesting**: Only 2 levels show in home tiles

## Common Workflows

### Temporarily Disable Section
1. Edit `content/section-name/index.md`
2. Add `hidden: true` to frontmatter
3. Section disappears from home page

### Reactivate Hidden Section
1. Edit the section's `index.md`
2. Remove `hidden: true` line
3. Section reappears on home page

### Reorganize Section Order
1. Identify current `date` values in frontmatter
2. Update dates to desired order (newer = higher)
3. Save files - order updates automatically

### Bulk Section Management
```bash
# Hide multiple sections
grep -r "^title:" content/*/index.md | while read file; do
  sed -i '/^---$/a hidden: true' "$file"
done

# Restore all hidden sections
find content -name "index.md" -exec sed -i '/^hidden: true$/d' {} \;
```

## Troubleshooting Common Issues

### Section Not Appearing
1. **Check file location**: Must be `content/section-name/index.md`
2. **Check frontmatter**: Must have `title` field
3. **Check hidden status**: Ensure no `hidden: true`
4. **Restart dev server**: Sometimes needed for new sections

### Icon Not Showing
1. **Check title match**: Icon mapping is case-sensitive
2. **Add mapping**: Edit [`HomeTile.vue:28-36`](theme/components/global/HomeTile.vue:28-36)
3. **Verify icon class**: Check icon library documentation

### Cover Image Not Loading
1. **Check path**: Must be relative to `content/public/`
2. **Check file exists**: Verify image file is in correct location
3. **Check format**: Use web-compatible formats (JPG, PNG, WebP)

### Wrong Section Order
1. **Check dates**: Verify `date` values in frontmatter
2. **Use ISO format**: `2024-01-01` format recommended
3. **Consistent dating**: All sections should have dates

For more troubleshooting help, see [Troubleshooting Guide](./troubleshooting.md).

## Next Steps

- **Configuration Options**: See [Configuration Reference](./configuration-reference.md)
- **Real Examples**: See [Examples](./examples.md)
- **System Understanding**: See [System Overview](./system-overview.md)