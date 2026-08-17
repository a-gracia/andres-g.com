## Why

The current site has a basic, template-like appearance that doesn't reflect professional credibility. The design uses generic blue/gray colors, plain sans-serif typography, and lacks visual hierarchy. A redesign will establish a distinctive, modern identity that balances the user's civil engineering background with their developer skillset.

## What Changes

- Complete visual redesign: dark theme with terracotta accent palette
- New typography: Space Grotesk (headings) + Inter (body) + JetBrains Mono (code/tags)
- Homepage restructure: hero section, featured project showcase, clean repo grid
- Component redesign: Header, Footer, ProjectCard, Navigation
- Image handling: fix Sharp dependency issue for Astro image optimization
- CSS overhaul: replace current global.css with new design system (plain CSS, no Tailwind)

## Capabilities

### New Capabilities

- `design-system`: Visual language — color palette, typography, spacing, component styles
- `homepage-layout`: Homepage structure — hero, featured project, repositories grid

### Modified Capabilities

<!-- None — no existing specs -->

## Impact

- **Files affected**: `global.css`, `BaseLayout.astro`, `Header.astro`, `Footer.astro`, `Navigation.astro`, `ProjectCard.astro`, `index.astro`, `BlogPost.astro`
- **Dependencies**: May need to install Sharp for image optimization, or configure passthrough image service
- **Pages**: homepage, blog, contact — all receive updated styling
- **No content changes**: Same pages, same data, new presentation
