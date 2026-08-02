---
name: Look up package and release intelligence
description: Retrieve Tidelift package metadata, release details, dependencies, SBOM, and vulnerability data for an open-source component.
api: openapi/tidelift-subscriber-api-openapi-original.json
operations:
  - showPackage
  - showRelease
  - releaseDependencies
  - releaseSbom
  - getVulnerability
  - getVulnerabilityAffectedReleases
---

# Look up package and release intelligence

Query Tidelift's package intelligence for a single component identified by
`{package_platform}/{package_name}` (e.g. `npm/lodash`, `pypi/django`).

## Auth
`Authorization: Bearer <TIDELIFT_API_KEY>` (user-scoped key is sufficient for lookups).

## Steps
1. `showPackage` — `GET /v1/packages/{package_platform}/{package_name}` for package-level
   metadata and quality checks (maintained, multiple maintainers, EOL, is-lifted).
2. `showRelease` — `GET /v1/packages/{package_platform}/{package_name}/releases/{release_version}`.
3. `releaseDependencies` — `.../releases/{release_version}/dependencies`.
4. `releaseSbom` — `.../releases/{release_version}/sbom` (CycloneDX).
5. For a CVE/advisory: `getVulnerability` — `GET /v1/vulnerabilities/{vulnerability_id}` and
   `getVulnerabilityAffectedReleases` for the affected-release list.

## Conventions & errors
- List endpoints (e.g. `getPackages`, `updates_since`) are page-number paginated.
- SBOM output is CycloneDX (JSON or XML per `Accept`).
- Errors use the `StandardError` envelope `{error, message, details?}`; handle 404 for
  unknown platform/package/version.
