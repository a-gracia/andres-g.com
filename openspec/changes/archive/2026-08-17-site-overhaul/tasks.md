## 1. Image Pipeline Fix

- [x] 1.1 Configure Astro passthrough image service in `astro.config.mjs`
- [x] 1.2 Verify `ProjectCard.astro` renders the Rischio.io screenshot without errors
- [x] 1.3 Run `npm run build` to confirm image pipeline works

## 2. Design System — CSS Foundation

- [x] 2.1 Replace `global.css` with new dark palette custom properties (#0A0A0B, #141414, #C75B39, etc.)
- [x] 2.2 Add Google Fonts imports: Space Grotesk, Inter, JetBrains Mono
- [x] 2.3 Define typography scale in CSS (headings 2-5rem, body 1rem, mono for tags)
- [x] 2.4 Define component styles: cards (border rgba(255,255,255,0.1), border-radius 10px), links (terracotta hover)
- [x] 2.5 Define responsive breakpoints (640px, 1024px)

## 3. BaseLayout Redesign

- [x] 3.1 Update `BaseLayout.astro` with new meta tags and page title ("Andrés Gracia")
- [x] 3.2 Apply dark background to body in layout or global.css

## 4. Header Redesign

- [x] 4.1 Restyle `Header.astro` — remove HR, use terracotta accent line
- [x] 4.2 Update name typography to Space Grotesk, large size
- [x] 4.3 Restyle profile picture with subtle border

## 5. Navigation Redesign

- [x] 5.1 Restyle `Navigation.astro` — mono font, uppercase, terracotta hover
- [x] 5.2 Simplify mobile nav (if hamburger exists, keep minimal)

## 6. Homepage Redesign

- [x] 6.1 Restructure `index.astro` with hero section (name + subtitle + links)
- [x] 6.2 Create featured project section with large screenshot
- [x] 6.3 Style `ProjectCard.astro` for featured project display
- [x] 6.4 Style repositories grid with responsive columns

## 7. Footer Redesign

- [x] 7.1 Restyle `Footer.astro` — minimal, mono font for links, terracotta accents

## 8. Blog & Contact Pages

- [x] 8.1 Verify `blog.astro` renders with new styles
- [x] 8.2 Verify `contact.astro` renders with new styles
- [x] 8.3 Verify individual blog posts (`MarkdownPostLayout.astro`) render correctly

## 9. Final Verification

- [x] 9.1 Run `npm run build` — all pages generate without errors
- [x] 9.2 Visual check: homepage, blog, contact pages look correct
- [x] 9.3 Responsive check: mobile, tablet, desktop layouts work
