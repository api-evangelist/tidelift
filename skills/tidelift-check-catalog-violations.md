---
name: Check catalog policy violations
description: List and triage open-source policy violations for a Tidelift catalog, then override a violation's status when justified.
api: openapi/tidelift-subscriber-api-openapi-original.json
operations:
  - authCheck
  - listCatalogsForOrg
  - listViolationsForCatalog
  - listViolationStatusOverrides
  - createViolationStatusOverride
---

# Check catalog policy violations

Use the Tidelift External API to review which dependencies violate an organization's
open-source standards (vulnerabilities, EOL, deprecation, license, up-to-date), then
record a status override when a violation is accepted or waived.

## Auth
All calls require `Authorization: Bearer <TIDELIFT_API_KEY>`. This flow needs an
organization- or user-scoped key. Verify the key first with `authCheck` (`GET /v1/authcheck`).

## Steps
1. `authCheck` — confirm the API key is valid.
2. `listCatalogsForOrg` — `GET /v1/{org_name}/catalogs` to find the target `catalog_name`.
3. `listViolationsForCatalog` — `GET /v1/{org_name}/catalogs/{catalog_name}/violations`.
   Results are paginated (page-number: `page`/`per_page`, `PaginationEnvelope`). Page through
   `next_page` until null.
4. For a violation you want to inspect, `listViolationStatusOverrides` —
   `GET /v1/{org_name}/catalogs/{catalog_name}/violations/{violation_id}/overrides`.
5. To accept/waive, `createViolationStatusOverride` —
   `POST /v1/{org_name}/catalogs/{catalog_name}/violations/{violation_id}/overrides`.

## Conventions & errors
- Org names may carry an org-type prefix (`team/abc`, `github/def`); default type is `team`.
- Errors return the `StandardError` envelope `{error, message, details?}` (see
  `errors/tidelift-problem-types.yml`) — handle 401/403 (key/scope), 404 (unknown catalog),
  422 (bad override body).
