## 1. Remove GitHub token from Header

- [x] 1.1 Remove `token` and `fetchOptions` variables from `Header.astro`
- [x] 1.2 Change fetch call to use no auth headers
- [x] 1.3 Add try/catch with fallback to empty object
- [x] 1.4 Ensure `userData` defaults to `{}` if fetch fails

## 2. Remove GitHub token from Repositories

- [x] 2.1 Remove `token` and `fetchOptions` variables from `Repositories.astro`
- [x] 2.2 Change fetch call to use no auth headers
- [x] 2.3 Add try/catch with fallback to empty array
- [x] 2.4 Default `githubReposData` to `[]` if fetch fails

## 3. Remove GitHub token from RepoLanguages

- [x] 3.1 Remove `token` and `fetchOptions` variables from `RepoLanguages.astro`
- [x] 3.2 Change fetch call to use no auth headers
- [x] 3.3 Add try/catch with fallback to empty object
- [x] 3.4 Default `languagesData` to `{}` if fetch fails

## 4. Verify

- [x] 4.1 Run `npm run build` without GITHUB_TOKEN set — should succeed
- [x] 4.2 Confirm no references to `GITHUB_TOKEN` remain in source
