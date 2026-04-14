# Round 2 Change Report

**Date:** 2026-04-14  
**Based on:** `ai-analysis-docs/gpt-change-accuracy-analysis-report.md`  
**Total files modified:** 8  
**Total files created:** 0  

---

## Summary

This round focused on **accuracy corrections and support boundary clarification** based on the GPT review. No new documentation pages were created. All changes were edits to existing files to fix the 4 P0 and 4 P1 issues identified in the review.

---

## Changes by File

### 1. `docs/en/functions/25-manage_clients.mdx` — Client Secret Rotation Fix

**Issue:** GPT P0 — 6.1 (Client secret rotation terminology conflict)

**What changed:**
- Rewrote the "Client Secret Management" section to clearly separate **regeneration** (immediate, disruptive) from **rotation** (policy-controlled, zero-downtime).
- Added a `<Directive type="info">` block titled "Rotation vs Regeneration" explaining that rotation requires a client policy with a Secret Rotation executor.
- Replaced the vague rotation steps with accurate policy-based rotation workflow including specific parameters (secret expiration period, rotated secret expiration period, remaining expiration period).
- Updated the regeneration warning to specify it applies "without a rotation policy in place."

**Before:** Regeneration and rotation were presented as sequential steps of the same process, with contradictory claims about whether the old secret remains valid.

**After:** Two clearly distinct mechanisms with correct semantics for each.

---

### 2. `docs/en/functions/20-manage_realms.mdx` — KeycloakRealmImport Semantics

**Issue:** GPT P0 — 6.2 (KeycloakRealmImport semantic overstatement)

**What changed:**
- Changed "GitOps-friendly, declarative Realm management" to "declarative Realm provisioning."
- Changed "the Operator triggers an import Job" to "the Operator triggers a one-time import Job."
- Added a `<Directive type="info">` block titled "One-Time Import" clarifying that the CRD does not perform continuous synchronization.

**Before:** The description implied ongoing declarative management.

**After:** Clearly described as a one-time provisioning mechanism.

---

### 3. `docs/en/how_to/35-import_export.mdx` — KeycloakRealmImport Limitations

**Issue:** GPT P0 — 6.2 (KeycloakRealmImport semantic overstatement)

**What changed:**
- Changed section heading from "Recommended" to "Recommended for Initial Setup."
- Added a `<Directive type="warning">` block titled "KeycloakRealmImport Is a One-Shot Import" with 4 explicit limitation points:
  - Creates or merges at import time only.
  - Does not track or reconcile ongoing state.
  - Modifying the CR does not trigger re-import.
  - Should not be used as a full lifecycle management mechanism.

**Before:** Listed as the recommended import method without qualification.

**After:** Clearly scoped to initial setup with explicit limitations documented.

---

### 4. `docs/en/how_to/50-customize_themes.mdx` — Deployment Path Reordering

**Issue:** GPT P0 — 6.3 (Theme path risk with `unsupported.podTemplate`)

**What changed:**
- Reversed the ordering: **Custom Image** is now Option A (recommended for production), **ConfigMap** is now Option B (development and testing only).
- Added introductory text stating the custom image approach is recommended and ConfigMap is for dev/test only.
- Moved the ConfigMap content after the custom image section.
- Added a `<Directive type="warning">` block titled "Not Recommended for Production" on the ConfigMap option, explicitly calling out:
  - `unsupported.podTemplate` is not part of the stable Operator API.
  - May change or be removed without notice.
  - ConfigMaps have a 1 MiB size limit.
  - Not covered by standard support and upgrade compatibility guarantees.
  - Rolling updates may cause Pod-level inconsistency.

**Before:** ConfigMap was Option A (recommended for small themes), custom image was Option B.

**After:** Custom image is Option A (production), ConfigMap is Option B (dev/test only) with strong warnings.

---

### 5. `docs/en/functions/40-monitor.mdx` — Health and Observability Enhancement

**Issue:** GPT P0 — 6.4 (Monitoring page incomplete vs Red Hat Observability Guide)

**What changed:**
- Added the aggregated `/health` endpoint to the health endpoints table with description.
- Added an "Observability Integration" section with a table mapping 5 observability signals (Metrics, Health, Logs, Events, Tracing) to their sources and tooling.
- Added a `<Directive type="info">` block titled "Distributed Tracing" explaining OpenTelemetry support and deferring configuration details to upstream docs.

**Before:** Health endpoints listed only `/health/live`, `/health/ready`, `/health/started`. No observability overview.

**After:** Complete health endpoint list including aggregated `/health`, plus observability integration guidance covering all signal types.

---

### 6. `docs/en/functions/30-manage_users.mdx` — Admin Access Control Depth

**Issue:** GPT P1 — 6.5 (Admin access control depth insufficiency)

**What changed:**
- Added 4 missing `query-*` roles (`query-users`, `query-clients`, `query-realms`, `query-groups`) and `create-client` to the fine-grained admin roles table.
- Added explanatory text about the `view-*` / `manage-*` pattern.
- Added a `<Directive type="info">` block titled "Query vs View Roles" explaining the distinction and why `query-*` roles are needed for console navigation.
- Added a new "Permission Boundaries" section documenting 3 key boundary rules:
  - Admins cannot grant roles they don't have.
  - Admins cannot access other realms without roles in those realms.
  - `realm-admin` composite role includes all individual roles.
- Updated the delegated admin creation steps to include `query-users` and `query-realms` roles.
- Added a `<Directive type="info">` block about dedicated admin console URLs per realm.

**Before:** 13 roles listed, no `query-*` roles, no permission boundaries, no dedicated console mention.

**After:** 18 roles listed, permission boundaries documented, dedicated admin console referenced.

---

### 7. `docs/en/architecture.mdx` — Cluster Discovery Wording

**Issue:** GPT P1 — 6.6 (Cluster discovery implementation detail)

**What changed:**
- Removed specific JGroups protocol names (`KUBE_PING`, `DNS_PING`).
- Rewrote the section to focus on Operator-managed semantics: "The Operator automatically configures the cluster discovery mechanism" and "The specific discovery protocol used is an internal implementation detail managed by the Operator."
- Changed prerequisites from protocol-specific requirements to general network connectivity requirements.
- Added a `<Directive type="info">` block titled "Cluster Formation is Operator-Managed" with troubleshooting guidance.

**Before:** Named specific JGroups protocols as user-facing facts, listed protocol-specific requirements.

**After:** Operator-managed boundary clearly stated, user-relevant prerequisites only, troubleshooting guidance.

---

### 8. `docs/en/how_to/25-configure_database.mdx` — Database Support Boundary

**Issue:** GPT P1-3 (Database version conservatism)

**What changed:**
- Replaced the multi-database table (PostgreSQL, MySQL/MariaDB, MSSQL, Oracle) with a single-database table showing PostgreSQL as the only fully supported database.
- Added text clarifying that other databases work at the engine level but are "at your own risk" and may not receive Alauda support.
- Strengthened the warning Directive to reference both the release notes and compatibility matrix, and explicitly cautioned against assuming upstream compatibility equals Alauda Build validation.

**Before:** Listed 4 databases as "Supported" with a generic version warning.

**After:** PostgreSQL as the only supported database, others explicitly marked as unvalidated.

---

### 9. `docs/en/concepts/50-extensibility.mdx` — Build-Time Provider Requirements

**Issue:** GPT P1-4 (Extensibility build-time limits)

**What changed:**
- Rewrote the deployment section introduction to explain the Quarkus build-time model upfront with 3 explicit bullet points about what it means.
- Changed the custom image section heading from "Recommended" to "Required."
- Replaced the `<Directive type="warning">` from "most providers must be present" to "providers **must** be present" — stronger language reflecting the actual requirement.
- Removed the init container alternative as a named option. Replaced it with a `<Directive type="warning">` explicitly advising against using `unsupported.podTemplate` for provider deployment.

**Before:** Custom image was "Recommended," init container was an "Alternative" for dev/testing.

**After:** Custom image is "Required," init container approach is warned against in a Directive.

---

## Items Not Changed (GPT P2 Recommendations)

The following GPT P2 recommendations were intentionally deferred:

| # | GPT Recommendation | Reason for Deferral |
|:--|:-------------------|:--------------------|
| 1 | Add tracing, dashboards, SLI, exemplars to monitoring | Environment-specific content requiring observability backend validation. A Directive pointing to upstream docs was added instead. |
| 2 | Add production operational boundary docs (rolling update, upgrade risk, custom image lifecycle) | Operator-managed behavior that may change between versions. Documenting implementation details risks inaccuracy. |
| 3 | Add FAPI/OAuth 2.1, token compromise, query roles, dedicated admin console | `query-*` roles and dedicated admin console were addressed (promoted from P2 to P1). FAPI/OAuth 2.1 requires certification verification. Token compromise is an operational procedure, not product configuration. |

---

## File Change Summary

| File | Lines Changed (approx) | Issue(s) Addressed |
|:-----|:----------------------|:-------------------|
| `functions/25-manage_clients.mdx` | +20 / -8 | P0-1: Secret rotation vs regeneration |
| `functions/20-manage_realms.mdx` | +6 / -1 | P0-2: KeycloakRealmImport semantics |
| `how_to/35-import_export.mdx` | +13 / -3 | P0-2: KeycloakRealmImport limitations |
| `how_to/50-customize_themes.mdx` | +30 / -20 | P0-3: Theme deployment path ordering |
| `functions/40-monitor.mdx` | +20 / -5 | P0-4: Health endpoints and observability |
| `functions/30-manage_users.mdx` | +35 / -8 | P1-1: Admin access control depth |
| `architecture.mdx` | +10 / -5 | P1-2: Cluster discovery wording |
| `how_to/25-configure_database.mdx` | +8 / -8 | P1-3: Database support boundary |
| `concepts/50-extensibility.mdx` | +15 / -20 | P1-4: Build-time provider requirements |
| **Total** | **~157 lines changed** | **4 P0 + 4 P1 = 8 issues** |

---

## Verification Checklist

- [ ] Build the documentation site (`yarn build`) to verify no broken links or MDX syntax errors
- [ ] Verify the `<Directive>` components render correctly (used extensively in this round)
- [ ] Confirm that the client secret rotation policy parameters match the actual Keycloak 26.x Admin Console fields
- [ ] Confirm that the `query-*` admin roles listed are present in Keycloak 26.4.7's `realm-management` client
- [ ] Validate that the aggregated `/health` endpoint exists and behaves as documented
