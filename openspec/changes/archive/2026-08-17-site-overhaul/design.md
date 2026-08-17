## Context

The current site uses Astro 6 with plain CSS, a basic blue/gray color scheme, and generic sans-serif typography. Components fetch GitHub data at build time. The image optimization pipeline is broken (missing Sharp dependency). See proposal.md for motivation.

## Goals / Non-Goals

**Goals:**
- Establish a distinctive dark-mode visual identity with terracotta accents
- Improve homepage layout with hero section and better project showcase
- Fix image rendering (Sharp dependency or passthrough)
- Keep plain CSS (no Tailwind) — site is small, no need for utility framework

**Non-Goals:**
- Adding new pages or features
- Changing content structure (blog posts, tags, RSS)
- Adding animations or complex interactions
- Changing the GitHub fetching strategy (starred repos stay)

## Decisions

### D1: Dark-only (no light mode toggle)

**Decision**: Single dark theme, no toggle.

**Rationale**: Personal site with 3 pages doesn't need theme switching. Dark-only simplifies CSS, eliminates flash-of-unstyled-content, and allows bolder contrast choices.

### D2: Terracotta as "copper tech" not "earth"

**Decision**: Use terracotta (#C75B39) as accent, styled as oxidized copper — technical and precise, not organic.

**Rationale**: Connects to geotech background while feeling digital. Paired with dark backgrounds, it reads as "precision instrument" not "soil."

**Alternatives considered**:
- Warm amber — too golden, less technical
- Pure red — too aggressive
- Muted brown — too earthy, loses tech feel

### D3: Three-font system

**Decision**: Space Grotesk (headings) + Inter (body) + JetBrains Mono (code/tags).

**Rationale**: Space Grotesk is geometric and technical without being cold. Inter is highly legible. JetBrains Mono signals "developer" in tags and code contexts. All three are free Google Fonts.

**Alternatives considered**:
- Single font family — loses personality
- System fonts — too generic for a portfolio
- Paid fonts — unnecessary for a personal site

### D4: CSS custom properties for design tokens

**Decision**: Define all colors, fonts, and spacing as CSS custom properties in `:root`.

**Rationale**: Single source of truth for the palette. Easy to adjust values later without hunting through rules. Standard practice for design systems.

### D5: Fix images via Astro passthrough service

**Decision**: Configure Astro to use passthrough image service instead of installing Sharp.

**Rationale**: Sharp adds a native dependency that can cause build issues. Passthrough is zero-config and sufficient for a personal site that doesn't need image optimization at build time. The `<Image>` component still provides type safety and alt text handling.

**Alternatives considered**:
- Install Sharp — adds build complexity, native dependency
- Use `<img>` directly — loses Astro image benefits (alt, width/height type safety)

### D6: Component-by-component refactor

**Decision**: Update each component individually (Header → Footer → Navigation → ProjectCard → BaseLayout → global.css) rather than rewriting everything at once.

**Rationale**: Incremental changes are easier to debug. Each component can be visually verified before moving to the next.

## Risks / Trade-offs

- **[Font loading performance]** → Three Google Fonts = 3 HTTP requests. Mitigation: Use `display=swap` for FOIT avoidance; all three fonts are small and commonly cached.
- **[Passthrough images]** → No build-time optimization (resizing, WebP conversion). Mitigation: Acceptable for a personal site; images are already appropriately sized.
- **[Dark mode accessibility]** → Some users prefer light mode. Mitigation: WCAG AA contrast ratios enforced in spec; dark-only is acceptable for a portfolio site.
