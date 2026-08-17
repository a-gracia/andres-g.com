## Purpose

Defines the visual language of the site — colors, typography, spacing, and component styling that create a cohesive dark-mode design with terracotta accents.

## ADDED Requirements

### Requirement: Dark color palette
The site SHALL use a dark background palette with light text and terracotta accents. Background colors SHALL be in the #0A0A0B to #1C1C1E range. Text colors SHALL provide WCAG AA contrast ratio (4.5:1 minimum) against backgrounds.

#### Scenario: Page renders with dark background
- **WHEN** any page loads
- **THEN** the background is #0A0A0B or darker, and body text is #E8E6E3 or lighter

#### Scenario: Accent color is terracotta
- **WHEN** accent elements render (links, highlights, interactive elements)
- **THEN** they use #C75B39 or #D4764E

### Requirement: Typography system
The site SHALL use three font families: Space Grotesk for headings, Inter for body text, and JetBrains Mono for code elements and tags. Font sizes SHALL establish clear hierarchy with headings at 2-5rem and body text at 1rem.

#### Scenario: Headings use Space Grotesk
- **WHEN** h1, h2, h3 elements render
- **THEN** they use the Space Grotesk font family

#### Scenario: Body text uses Inter
- **WHEN** paragraph and general text renders
- **THEN** it uses the Inter font family

#### Scenario: Code and tags use JetBrains Mono
- **WHEN** code elements, language tags, or technical labels render
- **THEN** they use the JetBrains Mono font family

### Requirement: Component styling
Interactive elements (links, buttons, cards) SHALL have hover states with terracotta color transitions. Cards SHALL have subtle borders (#FFFFFF at 10% opacity) and rounded corners.

#### Scenario: Link hover effect
- **WHEN** user hovers over a link
- **THEN** the link color transitions to terracotta (#C75B39) with a smooth transition

#### Scenario: Card appearance
- **WHEN** a card component renders
- **THEN** it has a border of rgba(255,255,255,0.1), border-radius of 8-12px, and background of #141414

### Requirement: Responsive layout
The site SHALL be responsive with a max-width container of 900-1100px centered on the page. On mobile (<640px), the layout SHALL stack vertically with reduced padding.

#### Scenario: Desktop layout
- **WHEN** viewport is wider than 640px
- **THEN** content is centered with max-width and horizontal padding

#### Scenario: Mobile layout
- **WHEN** viewport is narrower than 640px
- **THEN** layout stacks vertically with reduced gaps and padding
