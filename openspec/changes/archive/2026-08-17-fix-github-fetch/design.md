## Context

Three Astro components (`Header.astro`, `Repositories.astro`, `RepoLanguages.astro`) fetch GitHub API data at build time. Each independently reads `GITHUB_TOKEN` from environment and constructs its own `fetchOptions`. No error handling exists — if a fetch fails, the build breaks.

All fetching is build-time (Astro SSG), not runtime. This means:
- Rate limits apply per-build, not per-user-visit
- A personal site builds infrequently (manual deploys)
- 60 req/hour unauthenticated is plenty

## Goals / Non-Goals

**Goals:**
- Remove `GITHUB_TOKEN` dependency entirely
- Add error handling so failed fetches degrade to empty/missing data
- Keep changes minimal — fix the problem, don't restructure components

**Non-Goals:**
- Centralizing fetch logic into a shared utility (would be nice, but out of scope)
- Adding caching or build optimization
- Switching to GitHub GraphQL API
- Changing which repos are displayed (starred vs owned)

## Decisions

### D1: Remove auth headers entirely

**Decision**: Strip `Authorization` headers from all fetch calls. No token, no auth.

**Alternatives considered**:
- Keep optional token usage — rejected because the user explicitly wants no token dependency
- Use a shared fetch utility with optional auth — over-engineered for 3 call sites

**Rationale**: Unauthenticated limits (60 req/hour) are fine for infrequent builds. Removing the token eliminates a common failure mode (missing env var).

### D2: Wrap each fetch in try/catch with empty fallback

**Decision**: Each fetch gets a try/catch that returns empty data on failure.

```astro
---
let data = [];
try {
  const res = await fetch(url);
  if (res.ok) {
    data = await res.json();
  }
} catch {
  // fetch failed — render empty
}
---
```

**Alternatives considered**:
- Centralized error handler — unnecessary for 3 independent call sites
- Check `X-RateLimit-Remaining` header — adds complexity; a 403 response is sufficient signal

**Rationale**: Minimal, local error handling. Each component owns its failure mode. The `res.ok` check catches both network errors (caught by try/catch) and API errors (403, 404, 500).

### D3: Filter empty responses before mapping

**Decision**: Ensure data is an array before calling `.map()` — prevents crashes if the API returns an object (error response) or empty array.

**Rationale**: GitHub API returns `{ "message": "Not Found" }` on 404, which would crash `.map()`. Defaulting to `[]` handles this.

## Risks / Trade-offs

- **[Rate limit during dev]** → If iterating locally with many `astro dev` restarts, could hit 60 req/hour. Mitigation: dev server caches in `.astro/` — not a real problem unless cache is cleared frequently.
- **[Stale data]** → Repos fetched at build time, not live. Mitigation: acceptable for a personal site; rebuild to refresh.
- **[Silent failures]** → Errors are swallowed, not logged. Mitigation: acceptable for a personal site; if debugging is needed, add `console.error` in catch blocks.
