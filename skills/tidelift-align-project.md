---
name: Align a project against a catalog
description: Submit a project's dependencies for alignment against a Tidelift catalog and retrieve its bill of materials and alignment status.
api: openapi/tidelift-subscriber-api-openapi-original.json
operations:
  - authCheck
  - createProject
  - createProjectKey
  - performProjectAlignment
  - getProjectAlignmentStatus
  - getProjectBillOfMaterials
---

# Align a project against a catalog

Alignment evaluates a project's resolved dependencies against an organization's catalog
standards and returns violations plus a bill of materials.

## Auth
`Authorization: Bearer <TIDELIFT_API_KEY>`. Project creation needs an org/user key;
alignment submission uses a project-scoped key (mint one with `createProjectKey`).

## Steps
1. `authCheck` — validate the key.
2. `createProject` — `POST /v1/{org_name}/projects` (skip if the project exists).
3. `createProjectKey` — `POST /v1/{org_name}/projects/{project}/new-key` to get a project key.
4. `performProjectAlignment` — `POST /v1/catalog/{org_name}/{project}/alignment` with the
   dependency set (this is what the `tidelift` CLI `alignment save` runs under the hood).
5. `getProjectAlignmentStatus` — `GET /v1/{org_name}/projects/{project}/alignments/{revision}/status`
   until complete.
6. `getProjectBillOfMaterials` — `GET /v1/catalog/{org_name}/{project}/bill-of-materials`.

## Conventions & errors
- Alignment is asynchronous — poll status (`202 Accepted` then a terminal status).
- Errors use the `StandardError` envelope; handle 401/403 (scope), 404 (unknown project).
- Prefer the `tidelift` CLI (`cli/tidelift-cli.yml`) in CI; this skill is the raw-API path.
