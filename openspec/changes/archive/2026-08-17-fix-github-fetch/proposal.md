## Why

The GitHub API fetching in Header, Repositories, and RepoLanguages components requires a `GITHUB_TOKEN` environment variable. This creates friction for local development and deployment — builds fail silently if the token is missing. Unauthenticated GitHub API access (60 req/hour) is sufficient for a personal static site that builds infrequently.

## What Changes

- Remove `GITHUB_TOKEN` dependency from all GitHub-fetching components
- Add error handling so failed fetches degrade gracefully (empty state, not broken page)
- Consolidate token-reading logic (currently duplicated across 3 components)

## Capabilities

### New Capabilities

- `github-fetch`: Handles unauthenticated GitHub API calls with error handling and graceful degradation for user profile and repository data

### Modified Capabilities

<!-- None — no existing specs -->

## Impact

- **Components affected**: `Header.astro`, `Repositories.astro`, `RepoLanguages.astro`
- **Environment variables**: `GITHUB_TOKEN` no longer required
- **Rate limits**: Unauthenticated limit is 60 req/hour — sufficient for build-time fetching on a personal site
- **No runtime changes**: All fetching is Astro build-time (SSG)
