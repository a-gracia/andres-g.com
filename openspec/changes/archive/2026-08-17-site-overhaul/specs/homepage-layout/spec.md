## Purpose

Defines the structure and content of the homepage — hero section, featured project, and repositories grid that present the user's professional identity and work.

## ADDED Requirements

### Requirement: Hero section
The homepage SHALL display a hero section with the user's name in large bold text, a subtitle describing their role ("Civil Engineer who codes"), and links to GitHub, LinkedIn, and Blog. The hero SHALL be the first visual element on the page.

#### Scenario: Hero renders with name and subtitle
- **WHEN** the homepage loads
- **THEN** the user's name "Andrés Gracia" displays prominently, with a descriptive subtitle below it

#### Scenario: Hero includes social links
- **WHEN** the hero section renders
- **THEN** it includes clickable links to GitHub, LinkedIn, and Blog

### Requirement: Featured project section
The homepage SHALL display the Rischio.io project with a large screenshot, project name, description, and a "Visit" link. The screenshot SHALL render correctly without image processing errors.

#### Scenario: Featured project displays with image
- **WHEN** the homepage loads
- **THEN** the Rischio.io project shows a screenshot, name, description, and visit link

#### Scenario: Project image renders without errors
- **WHEN** the featured project image loads
- **THEN** it displays successfully without Sharp or image processing errors

### Requirement: Repositories grid
The homepage SHALL display starred GitHub repositories in a responsive grid layout. Each repository card SHALL show the repo name, description, star count, and primary language. The grid SHALL adapt from 3 columns on desktop to 1 column on mobile.

#### Scenario: Repository grid displays repos
- **WHEN** the homepage loads
- **THEN** starred repositories appear in a grid with name, description, stars, and language

#### Scenario: Repository grid is responsive
- **WHEN** viewport changes width
- **THEN** the grid adjusts columns: 3 on desktop, 2 on tablet, 1 on mobile

### Requirement: Page title
The homepage SHALL display a page title that matches the user's professional identity, used in the browser tab and meta tags.

#### Scenario: Browser tab shows title
- **WHEN** the homepage loads
- **THEN** the browser tab displays "Andrés Gracia" or similar professional title
