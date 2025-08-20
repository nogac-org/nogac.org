# Examples: Real-World Examples and Copy-Paste Templates

This guide provides practical, copy-paste ready examples for common scenarios in the VitePress index page system. All examples are based on real implementations and tested patterns.

## Basic Section Templates

### 1. Standard Section Template
**Use for:** Most common sections with title, description, and cover image

**File:** `content/my-section/index.md`
```yaml
---
title: My Section
description: Clear, concise description that appears on the home page tile
cover: /media/my-section/cover.jpg
date: 2024-01-01
---

# My Section

Welcome to this section of the site. This content appears when users click the tile.

## What You'll Find Here
- Feature or topic 1
- Feature or topic 2  
- Feature or topic 3

## Getting Started
Instructions or next steps for users...
```

**Result:** Creates a tile with title, description, cover image background, and dynamic color.

---

### 2. Section with Child Pages
**Use for:** Sections that contain multiple sub-topics

**Directory Structure:**
```
content/
└── music-theory/
    ├── index.md          # Main section page
    ├── harmony/
    │   └── index.md     # Child page (appears in sub-nav)
    ├── melody/
    │   └── index.md     # Child page (appears in sub-nav)
    └── rhythm/
        └── index.md     # Child page (appears in sub-nav)
```

**File:** `content/music-theory/index.md`
```yaml
---
title: Music Theory
description: Explore the fundamental concepts of harmony, melody, and rhythm
cover: /media/theory/cover.jpg
date: 2024-03-01
---

# Music Theory

Master the building blocks of music through interactive lessons and examples.

## Topics Covered
The sub-sections below will appear as links in the home page tile:
```

**Child Page:** `content/music-theory/harmony/index.md`
```yaml
---
title: Harmony
description: Understanding chord progressions and harmonic relationships
cover: /media/theory/harmony-cover.jpg
date: 2024-03-02
---

# Harmony

Learn how chords work together to create musical progressions...
```

**Result:** Main tile shows "Music Theory" with sub-navigation links for Harmony, Melody, and Rhythm.

---

### 3. Hidden/Draft Section
**Use for:** Work-in-progress content that shouldn't appear publicly

**File:** `content/draft-section/index.md`
```yaml
---
title: Draft Section
description: This section is under development
cover: /media/drafts/placeholder.jpg
date: 2024-01-01
hidden: true
---

# Draft Section

This content is being developed and won't appear on the home page.

## TODO
- [ ] Add main content
- [ ] Create cover image
- [ ] Review and publish
```

**Result:** Section exists but doesn't appear on home page. Remove `hidden: true` to make visible.

---

## Advanced Section Examples

### 4. Section with Custom Icon
**Use for:** Sections needing specific visual branding

**Step 1:** Create section file `content/resources/index.md`
```yaml
---
title: Resources
description: Helpful tools and reference materials
cover: /media/resources/cover.jpg
date: 2024-02-01
---

# Resources

Curated collection of helpful tools and references...
```

**Step 2:** Add icon mapping in [`theme/components/global/HomeTile.vue:28-36`](theme/components/global/HomeTile.vue:28-36)
```vue
.i-la-book(v-if="item?.frontmatter?.title == 'Theory'")
.i-la-hand-point-up(v-if="item?.frontmatter?.title == 'Practice'")
.i-la-tools(v-if="item?.frontmatter?.title == 'Resources'")  <!-- Add this line -->
```

**Available Icon Libraries:**
- Line Awesome: `i-la-*` (e.g., `i-la-star`, `i-la-book`, `i-la-music`)
- Material Design: `i-mdi-*` (e.g., `i-mdi-music`, `i-mdi-piano`)
- Boxicons: `i-bxs-*` (e.g., `i-bxs-music`, `i-bxs-star`)

**Result:** Section appears with custom tools icon instead of default.

---

### 5. Iframe Embedded Application
**Use for:** External tools or applications

**File:** `content/tools/tuner/index.md`
```yaml
---
layout: iframe
iframe: https://tuner.chromatone.center
title: Chromatic Tuner
description: Online instrument tuner with visual feedback
---

# Chromatic Tuner

This page embeds our external tuning application.
```

**Result:** Full-page iframe embedding the external tuner application.

---

### 6. Section with Top Content
**Use for:** Sections where intro content should appear before child navigation

**File:** `content/tutorials/index.md`
```yaml
---
title: Tutorials
description: Step-by-step learning guides
cover: /media/tutorials/cover.jpg
date: 2024-04-01
topContent: true
---

# Tutorials

This introductory content appears BEFORE the child page navigation.

## How to Use These Tutorials
1. Start with basics
2. Practice each concept
3. Move to advanced topics

<!-- Child page links appear AFTER this content -->
```

**Result:** Introduction content renders above child page navigation instead of below.

---

## Real-World Section Examples

### 7. Practice Section (From Actual Site)
**Based on:** [`content/practice/index.md`](content/practice/index.md)

```yaml
---
title: Practice
description: Interactive tools for hands-on music exploration
cover: /media/practice/cover.jpg
date: 2022-01-01
---

# Practice

Hands-on interactive music tools to explore concepts through practice.

## Categories
<!-- Child sections automatically appear as sub-navigation -->
```

**Child Sections Include:**
- `content/practice/chord/index.md` - Chord tools
- `content/practice/rhythm/index.md` - Rhythm tools  
- `content/practice/scale/index.md` - Scale tools
- `content/practice/midi/index.md` - MIDI tools

---

### 8. Theory Section (From Actual Site)  
**Based on:** Existing section structure

```yaml
---
title: Theory  
description: Visual music theory concepts with interactive explanations
cover: /media/theory/cover.jpg
date: 2023-01-01
---

# Theory

Learn music theory through visual and interactive explanations.

## Core Concepts
The following topics are covered in this section...
```

---

## Home Page Examples

### 9. Main Home Page Configuration
**Based on:** [`content/index.md`](content/index.md)

```yaml
---
layout: home
title: Musetta Stone
description: Visual Music Language Research Hub
youtube: qthKClCRIl0
date: 2017-01-01
---

<a href="/theory/interplay/spectrum/"><img class="w-full" alt="Chromatic Spectrum" src="/img/spectrum.svg" /></a>

Musetta Stone is an open source research and design project...

## Features
- Visual music education
- Interactive web applications  
- Community learning programs
```

**Result:** Home page with YouTube embed and section tiles arranged automatically.

---

### 10. Simple Home Page
**Use for:** Minimal home page setup

```yaml
---
layout: home
title: My Music Site
description: Learning music through technology
date: 2024-01-01
---

# Welcome

Explore music through interactive tools and lessons.
```

**Result:** Clean home page with just section tiles, no video embed.

---

## Before/After Scenarios

### 11. Adding a New Section

**Before:** Only existing sections visible
```
content/
├── theory/index.md
├── practice/index.md  
└── index.md (home)
```

**After:** Add new section
```bash
# Create directory
mkdir content/resources

# Create index file
cat > content/resources/index.md << EOF
---
title: Resources
description: Helpful tools and reference materials
cover: /media/resources/cover.jpg
date: 2024-01-01
---

# Resources

Collection of helpful tools and references.
EOF
```

**Result:** New "Resources" tile appears automatically on home page.

---

### 12. Hiding Seasonal Content

**Before:** Holiday section visible year-round
```yaml
---
title: Holiday Music
description: Festive songs and arrangements
cover: /media/holiday/cover.jpg
date: 2024-12-01
---
```

**After:** Hide during off-season
```yaml
---
title: Holiday Music
description: Festive songs and arrangements  
cover: /media/holiday/cover.jpg
date: 2024-12-01
hidden: true    # Add this line
---
```

**Result:** Section hidden from home page but content preserved.

---

### 13. Reordering Sections

**Before:** Sections in alphabetical order by title
```yaml
# content/advanced/index.md
date: 2024-01-01

# content/beginner/index.md  
date: 2024-01-02

# content/intermediate/index.md
date: 2024-01-03
```

**After:** Logical learning order (beginner first)
```yaml
# content/beginner/index.md - appears first
date: 2024-01-03

# content/intermediate/index.md - appears second  
date: 2024-01-02

# content/advanced/index.md - appears third
date: 2024-01-01
```

**Result:** Sections reordered by difficulty level instead of alphabetically.

---

## Copy-Paste Templates

### 14. Quick Section Starter
**Copy-paste ready template for new sections:**

```bash
# Create directory
mkdir content/SECTION_NAME

# Create index file  
cat > content/SECTION_NAME/index.md << 'EOF'
---
title: SECTION_TITLE
description: SECTION_DESCRIPTION  
cover: /media/SECTION_NAME/cover.jpg
date: 2024-01-01
---

# SECTION_TITLE

SECTION_CONTENT_GOES_HERE

## Features
- Feature 1
- Feature 2
- Feature 3
EOF
```

**Replace:**
- `SECTION_NAME` with directory name (kebab-case)
- `SECTION_TITLE` with display title
- `SECTION_DESCRIPTION` with brief description
- `SECTION_CONTENT_GOES_HERE` with actual content

---

### 15. Child Page Template
**For adding sub-pages to existing sections:**

```bash
# Create child directory
mkdir content/PARENT_SECTION/CHILD_NAME

# Create child index
cat > content/PARENT_SECTION/CHILD_NAME/index.md << 'EOF'
---
title: CHILD_TITLE
description: CHILD_DESCRIPTION
cover: /media/PARENT_SECTION/CHILD_NAME/cover.jpg
date: 2024-01-01
---

# CHILD_TITLE

CHILD_CONTENT_GOES_HERE
EOF
```

---

### 16. Bulk Section Creation Script
**For creating multiple sections at once:**

```bash
#!/bin/bash
# Create multiple sections

sections=("basics" "intermediate" "advanced")
base_date="2024-01-0"

for i in "${!sections[@]}"; do
  section=${sections[$i]}
  date="${base_date}$((i+1))"
  
  mkdir -p "content/$section"
  cat > "content/$section/index.md" << EOF
---
title: ${section^}
description: ${section^} level content and exercises
cover: /media/$section/cover.jpg  
date: $date
---

# ${section^}

Content for $section level students.
EOF
done
```

---

## Icon Reference Examples

### 17. Common Icon Mappings
**Copy-paste ready icon assignments for [`HomeTile.vue`](theme/components/global/HomeTile.vue):**

```vue
<!-- Educational Icons -->
.i-la-book(v-if="item?.frontmatter?.title == 'Theory'")
.i-la-graduation-cap(v-if="item?.frontmatter?.title == 'Academy'")  
.i-la-chalkboard-teacher(v-if="item?.frontmatter?.title == 'Tutorship'")

<!-- Activity Icons -->
.i-la-hand-point-up(v-if="item?.frontmatter?.title == 'Practice'")
.i-la-play-circle(v-if="item?.frontmatter?.title == 'Examples'")
.i-la-tools(v-if="item?.frontmatter?.title == 'Tools'")

<!-- Content Icons -->  
.i-la-file-alt(v-if="item?.frontmatter?.title == 'Resources'")
.i-la-download(v-if="item?.frontmatter?.title == 'Downloads'")
.i-la-external-link-alt(v-if="item?.frontmatter?.title == 'Links'")

<!-- Community Icons -->
.i-la-users(v-if="item?.frontmatter?.title == 'Community'")
.i-la-comments(v-if="item?.frontmatter?.title == 'Discussion'")
.i-la-at(v-if="item?.frontmatter?.title == 'Contacts'")

<!-- Commercial Icons -->
.i-la-shopping-bag(v-if="item?.frontmatter?.title == 'Shop'")
.i-la-star(v-if="item?.frontmatter?.title == 'Support'")
.i-la-credit-card(v-if="item?.frontmatter?.title == 'Pricing'")
```

---

## Testing Your Examples

### 18. Verification Checklist
After implementing any example:

```bash
# 1. Check file structure
ls -la content/your-section/

# 2. Verify frontmatter syntax  
head -10 content/your-section/index.md

# 3. Test development server
npm run dev
# Visit http://localhost:3000

# 4. Check for errors in console
# Open browser dev tools, look for errors

# 5. Test production build
npm run build
```

### 19. Common Issues and Quick Fixes

**Issue:** Section not appearing on home page
**Fix:** Check frontmatter has required fields:
```yaml
---
title: Required
description: Required  
date: Required
# No hidden: true
---
```

**Issue:** Cover image not loading  
**Fix:** Verify image path and file exists:
```bash
# Check if image exists
ls -la content/public/media/section/cover.jpg

# Correct path format
cover: /media/section/cover.jpg  # Note: starts with /
```

**Issue:** Child pages not showing in tile
**Fix:** Ensure child directories have `index.md` files:
```bash
# Each child needs index.md
mkdir content/parent/child
echo "---\ntitle: Child\n---\n# Child" > content/parent/child/index.md
```

For more troubleshooting help, see [Troubleshooting Guide](./troubleshooting.md).

## Next Steps

- **Understanding System**: [System Overview](./system-overview.md)  
- **Configuration Details**: [Configuration Reference](./configuration-reference.md)
- **File Relationships**: [File Structure](./file-structure.md)
- **Problem Solving**: [Troubleshooting](./troubleshooting.md)