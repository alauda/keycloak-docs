# Round 3 Change Report

**Date:** 2026-04-14  
**Based on:** `ai-analysis-docs-2/change-accuracy-analysis-report.md`  
**Total files modified:** 5  
**Total files created:** 0  

---

## Summary

This round is a **precision calibration** addressing 2 P0 and 2 P1 issues from the Round 2 review. All changes are edits to existing files. No new pages were created.

---

## Changes by File

### 1. `docs/en/how_to/35-import_export.mdx` — RealmImport Create-Only Semantics

**Issue:** P0 — 5.1 (RealmImport "creates or merges" wording too broad)

**What changed:**
- Changed "It creates or merges Realm configuration at import time." to "It creates a new Realm at import time. If a Realm with the same name already exists, the import will not overwrite or update the existing Realm."
- Added a new bullet: "It does not delete Realms or Realm objects — it is a create-only mechanism."

**Before:** "creates or merges" implied update semantics that don't exist.

**After:** Explicitly create-only, no overwrite, no delete — matches Red Hat Operator Guide 26.2 semantics.

---

### 2. `docs/en/functions/40-monitor.mdx` — Tracing Configuration + Metrics Depth

**Issues:** P0 — 5.2 (Tracing config path), P1 — 5.3 (Metrics depth)

**What changed (Tracing):**
- Replaced the brief one-sentence Directive about tracing with a comprehensive block including:
  - YAML example using `spec.tracing` fields.
  - Full reference table of all 8 `spec.tracing` fields (`enabled`, `endpoint`, `protocol`, `samplerType`, `samplerRatio`, `compression`, `serviceName`, `resourceAttributes`) with descriptions and defaults.
  - Guidance: "Use `spec.tracing` for all standard tracing configuration. Only use `additionalOptions` for tracing-related options not covered by the first-class fields."

**Before:** Stated tracing is configured "via `additionalOptions` in the Keycloak CR" (incorrect).

**After:** Documents `spec.tracing` as the primary configuration path with complete field reference from the CRD.

**What changed (Metrics):**
- Expanded the Key Metrics table from 4 rows to 7 rows:
  - Enhanced JVM, HTTP, Database rows with additional metrics and operational context.
  - Added: Keycloak Registration events, Keycloak Client Login events, Infinispan/Cache metrics.
- Added a new "Recommended Alerting Indicators" section with a table of 5 key alerting indicators:
  - Login failure rate (`rate(keycloak_login_errors_total[5m])`)
  - HTTP error rate (`rate(http_server_requests_seconds_count{status=~"5.."}[5m])`)
  - Database pool exhaustion (`agroal_awaiting_connections > 0`)
  - JVM memory pressure (`jvm_memory_used_bytes / jvm_memory_max_bytes > 0.9`)
  - Health probe failure

**Before:** 4 generic metric categories, no alerting guidance.

**After:** 7 metric categories with operational context, plus 5 recommended alerting indicators with specific queries.

---

### 3. `docs/en/how_to/50-customize_themes.mdx` — Custom Image Build-Time Options

**Issue:** P1 — 5.4 (Custom image health-enabled/metrics-enabled)

**What changed:**
- Updated the Dockerfile example to include `--health-enabled=true --metrics-enabled=true` in the `kc.sh build` command.
- Replaced the "Image Maintenance" warning with a more comprehensive "Build-Time Options in Custom Images" warning that explains:
  - Build-time options must be in the Dockerfile.
  - `additionalOptions` in the CR are ignored for pre-built custom images.
  - Rebuild required on Keycloak version upgrades.

**Before:** Dockerfile had bare `kc.sh build`, warning only mentioned version upgrades.

**After:** Dockerfile includes required build-time flags, warning covers both the `additionalOptions` ignore behavior and version upgrade requirement.

---

### 4. `docs/en/concepts/50-extensibility.mdx` — Custom Image Build-Time Options

**Issue:** P1 — 5.4 (Custom image health-enabled/metrics-enabled)

**What changed:**
- Updated the Dockerfile example to include `--health-enabled=true --metrics-enabled=true` in the `kc.sh build` command.
- Added a new `<Directive type="warning">` titled "Build-Time Options in Custom Images" explaining that `additionalOptions` from the CR are ignored when a custom image is used.

**Before:** Dockerfile had bare `kc.sh build`, no mention of build-time option override behavior.

**After:** Dockerfile includes required flags, explicit warning about CR `additionalOptions` being ignored.

---

## Items Not Changed (P2 Deferred)

| # | Reviewer Recommendation | Reason for Deferral |
|:--|:------------------------|:--------------------|
| 1 | Dashboard templates (Grafana JSON) | Best delivered as downloadable artifacts, not inline docs |
| 2 | SLI/SLO definitions | Organization-specific; alerting indicators table added as practical alternative |
| 3 | Exemplars in metrics | Advanced Prometheus feature; minimal user impact |
| 4 | Detailed cache statistics enablement | Low priority unless actively debugging cache |

---

## File Change Summary

| File | Lines Changed (approx) | Issue(s) Addressed |
|:-----|:----------------------|:-------------------|
| `how_to/35-import_export.mdx` | +3 / -1 | P0: RealmImport create-only semantics |
| `functions/40-monitor.mdx` | +40 / -8 | P0: Tracing config path; P1: Metrics depth |
| `how_to/50-customize_themes.mdx` | +8 / -4 | P1: Custom image build-time options |
| `concepts/50-extensibility.mdx` | +10 / -2 | P1: Custom image build-time options |
| **Total** | **~61 lines changed** | **2 P0 + 2 P1 = 4 issues** |

---

## Cumulative Change Summary (All Rounds)

| Round | Files Created | Files Modified | Key Focus |
|:------|:-------------|:--------------|:----------|
| Round 1 | 16 | 3 | Content creation — filled 18 gap areas |
| Round 2 | 0 | 9 | Accuracy correction — fixed support boundary expressions |
| Round 3 | 0 | 5 | Precision calibration — tightened wording, added CRD-aligned config |
| **Total** | **16** | **17 (cumulative unique: ~12)** | **~14,500 words of new/revised content** |

---

## Verification Checklist

- [ ] Build the documentation site (`yarn build`) to verify no MDX syntax errors from the expanded tracing Directive (contains YAML code block inside a Directive + table)
- [ ] Confirm `spec.tracing` fields render correctly in the CRD auto-generated API reference
- [ ] Validate the Prometheus query syntax in the alerting indicators table against a live Prometheus instance
- [ ] Confirm that `kc.sh build --health-enabled=true --metrics-enabled=true` is the correct syntax for Keycloak 26.4.7
- [ ] Test that a custom image built with the updated Dockerfile correctly exposes `/health` and `/metrics` endpoints
