## Purpose

Provides unauthenticated GitHub API access for fetching user profile and repository data during Astro builds, with graceful degradation when API calls fail or rate limits are hit.

## Requirements

### Requirement: Unauthenticated GitHub API access
The system SHALL fetch GitHub data without requiring authentication tokens. All GitHub API calls SHALL use unauthenticated requests.

#### Scenario: Build succeeds without GITHUB_TOKEN
- **WHEN** the site builds without a GITHUB_TOKEN environment variable set
- **THEN** all GitHub data fetches complete successfully using unauthenticated requests

#### Scenario: Token is not used
- **WHEN** a GITHUB_TOKEN environment variable exists
- **THEN** the system SHALL NOT use it for any GitHub API calls

### Requirement: Graceful error handling
The system SHALL handle GitHub API failures without breaking the page render. Failed fetches SHALL result in empty/missing data, not build errors.

#### Scenario: User profile fetch fails
- **WHEN** the GitHub user profile API returns an error or is unreachable
- **THEN** the header renders without the profile picture and the page builds successfully

#### Scenario: Repository list fetch fails
- **WHEN** the GitHub starred repos API returns an error or is unreachable
- **THEN** the repositories section renders empty and the page builds successfully

#### Scenario: Repository languages fetch fails
- **WHEN** a repository languages API call returns an error or is unreachable
- **THEN** that repository renders without language tags and the page builds successfully

### Requirement: Rate limit awareness
The system SHALL handle GitHub API rate limit responses (HTTP 403 with `X-RateLimit-Remaining: 0`) gracefully.

#### Scenario: Rate limit exceeded during build
- **WHEN** GitHub API returns a rate limit error (403)
- **THEN** the affected component renders with empty/missing data and the build succeeds
