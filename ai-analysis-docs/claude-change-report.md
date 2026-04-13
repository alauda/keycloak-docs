# Documentation Improvement Change Report

**Date:** 2026-04-13  
**Based on:** gap-analysis-report.md  
**Total files created:** 16  
**Total files modified:** 3  

---

## Summary of Changes

| Type | Count |
|:-----|:------|
| New documentation pages created | 16 |
| Existing pages extended | 3 |
| New documentation sections added | 1 (`integration/`) |
| Estimated new content | ~12,000 words |

---

## New Files Created

### P0 — Critical Gaps

| # | File | Gap | Description |
|:--|:-----|:----|:------------|
| 1 | `docs/en/functions/25-manage_clients.mdx` | GAP-02 | Comprehensive client management guide: OIDC/SAML client creation, client secret management/rotation, protocol mappers, client scopes (default vs optional), service accounts, client policies (FAPI profiles). |
| 2 | `docs/en/functions/35-configure_identity_providers.mdx` | GAP-03 | Identity provider brokering guide: OIDC IdP, SAML IdP, social providers (GitHub example), IdP mappers, first login flow customization, default IdP, broker logout. |
| 3 | `docs/en/functions/50-configure_authentication.mdx` | GAP-01 | Authentication configuration guide: password policies (14 policy types), OTP policies (TOTP/HOTP), authentication flows (built-in and custom), WebAuthn/passwordless, X.509 certificate auth, Kerberos/SPNEGO, step-up authentication (ACR/LoA), user session limits. |
| 4 | `docs/en/how_to/20-configure_ldap_federation.mdx` | GAP-04 | Step-by-step LDAP/AD federation guide: connection setup, user search config, edit modes, synchronization, LDAP mappers (attribute, group, role), TLS/SSL configuration, troubleshooting table. |

### P1 — Important Gaps

| # | File | Gap | Description |
|:--|:-----|:----|:------------|
| 5 | `docs/en/integration/index.mdx` | GAP-05 | New section index for application integration guides. |
| 6 | `docs/en/integration/10-secure_app_with_oidc.mdx` | GAP-05 | OIDC application integration guide: recommended libraries by platform, authorization code flow with PKCE, securing REST APIs, logout (RP-initiated, backchannel), token refresh, other grant types. |
| 7 | `docs/en/integration/20-oidc_endpoints_reference.mdx` | GAP-05 | OIDC/OAuth2 endpoints reference: all core endpoints, device/CIBA endpoints, SAML endpoints, token auth methods, supported scopes with claims. |
| 8 | `docs/en/concepts/40-authorization_services.mdx` | GAP-06 | Authorization services concepts: architecture, key concepts (resource server, resource, scope, permission, policy, RPT), 9 policy types, decision strategies, authorization flow, UMA overview. |
| 9 | `docs/en/functions/60-configure_authorization.mdx` | GAP-06 | Authorization services step-by-step: define resources/scopes, create policies (role, group, time, user, aggregated), create permissions (resource/scope-based), test with evaluation tool, RPT API examples. |
| 10 | `docs/en/how_to/40-security_hardening.mdx` | GAP-08 + GAP-15 | Security hardening guide: brute force detection, admin endpoint protection (NetworkPolicy, separate hostnames), redirect URI validation, clickjacking protection, token security (audience, lifespan, rotation), SSL enforcement, CSRF, read-only user attributes, vault integration, security checklist. |
| 11 | `docs/en/functions/55-manage_sessions.mdx` | GAP-09 | Session management guide: view sessions (realm/user/client), terminate sessions, revocation policy, SSO session timeouts, client session timeouts, token timeouts, offline sessions, Remember Me. |
| 12 | `docs/en/how_to/50-customize_themes.mdx` | GAP-10 | Theme customization guide: theme types, directory structure, theme.properties, custom CSS, text label overrides, Kubernetes deployment (ConfigMap and custom image approaches), email template customization, i18n. |
| 13 | `docs/en/apis/rest_api/index.mdx` | GAP-11 | Admin REST API reference: authentication methods (password, service account), common operations (realms, users, clients, roles, groups, IdPs, events) with curl examples, pagination, error codes. |

### P2 — Nice-to-Have Gaps

| # | File | Gap | Description |
|:--|:-----|:----|:------------|
| 14 | `docs/en/how_to/25-configure_database.mdx` | GAP-12 | Database configuration guide: supported databases table, Keycloak CR configuration reference, connection pool sizing, HA recommendations, backup strategy guidance, schema migration notes. |
| 15 | `docs/en/how_to/35-import_export.mdx` | GAP-13 | Import/export guide: partial export (Admin Console), full export (CLI), REST API export, CRD import, CLI import, environment migration workflow. |
| 16 | `docs/en/concepts/35-account_console.mdx` | GAP-18 | Account console overview: self-service features, password change, OTP setup, WebAuthn registration, device activity, administrator controls. |
| 17 | `docs/en/concepts/50-extensibility.mdx` | GAP-17 | Extensibility overview: SPI extension points table, development pattern, Kubernetes deployment (custom image build, init container alternative), versioning notes. |

---

## Existing Files Modified

| # | File | Gap | Changes |
|:--|:-----|:----|:--------|
| 1 | `docs/en/functions/40-monitor.mdx` | GAP-07 | **Extended** with Event Auditing section (~70 lines): login event types, admin events, event listeners (jboss-logging, email), REST API event queries. |
| 2 | `docs/en/functions/30-manage_users.mdx` | GAP-14 | **Extended** with Admin Access Control section (~40 lines): master vs realm-specific admins, fine-grained admin roles table (13 roles), delegated admin creation steps. |
| 3 | `docs/en/architecture.mdx` | GAP-16 | **Extended** with Clustering and Cache Architecture section (~40 lines): embedded Infinispan overview, cache types table (6 caches), custom cache configuration via ConfigMap, cluster discovery (KUBE_PING/DNS_PING). |

---

## Items That Could Not Be Determined

The following items were flagged in the gap analysis but could not be fully resolved without additional verification. They are documented with warnings/directives in the relevant pages:

| # | Item | Location | Reason |
|:--|:-----|:---------|:-------|
| 1 | **Exact database version compatibility matrix** | `how_to/25-configure_database.mdx` | The supported database versions for Alauda Build of Keycloak may differ from upstream Keycloak. A `<Directive type="warning">` advises users to check release notes. |
| 2 | **Truststore SPI configuration for LDAP TLS** | `how_to/20-configure_ldap_federation.mdx` | The exact truststore SPI options (`spi-truststore-file-*`) may vary between Keycloak versions. A `<Directive type="warning">` advises verification against upstream docs for the specific version. |
| 3 | **Custom Keycloak image base tag** | `how_to/50-customize_themes.mdx`, `concepts/50-extensibility.mdx` | The Dockerfile examples use `quay.io/keycloak/keycloak:26.4.7`. Alauda Build may use a different base image registry/path. Update the image reference if Alauda uses a custom registry. |
| 4 | **Init container provider hot-deployment support** | `concepts/50-extensibility.mdx` | Whether runtime provider JAR mounting works without `kc.sh build` depends on the specific SPI type. A `<Directive type="warning">` notes this limitation. |
| 5 | **Infinispan cache configuration XML format** | `architecture.mdx` | The cache XML format changes between Keycloak versions. A `<Directive type="warning">` advises testing custom configurations. |
| 6 | **WebAuthn Admin Console UI flow** | `functions/50-configure_authentication.mdx` | The exact steps for adding WebAuthn steps to authentication flows may differ in the Keycloak 26.x Admin Console compared to the documented procedure. Steps are based on the standard Keycloak flow editor. |
| 7 | **Client Policies built-in profiles** | `functions/25-manage_clients.mdx` | The FAPI profiles listed are based on upstream Keycloak. Alauda Build availability should be confirmed. |
| 8 | **SSSD/FreeIPA integration** | Not documented | RH-SSO documents SSSD/FreeIPA federation. This is specific to Red Hat's identity stack. Relevance to Alauda customers is unclear. Omitted pending confirmation. |
| 9 | **Kerberos detailed setup** | `functions/50-configure_authentication.mdx` | A `<Directive type="info">` notes that Kerberos setup details are environment-specific and defers to upstream documentation. |
| 10 | **Docker Registry v2 authentication** | Not documented | RH-SSO documents Docker registry protocol support. This is a niche feature. Omitted as low priority. |

---

## Items Intentionally Not Documented (RH-SSO Features Not Applicable to Keycloak 26.x)

| Item | Reason |
|:-----|:-------|
| WildFly/JBoss-based installation (standalone, domain mode) | Keycloak 26.x is Quarkus-based; these modes do not exist. |
| Legacy client adapters (Java EAP, Fuse, servlet filter, JS, Node.js) | Deprecated in Keycloak 26.x. Documented the modern approach (standard OIDC libraries) instead. |
| OpenShift Templates and Image Streams | Alauda Build uses a Kubernetes Operator. Already documented. |
| RPM-based installation | Not applicable to containerized deployment. |
| mod_auth_mellon (Apache HTTPD SAML module) | Legacy integration. Not relevant for modern deployments. |
| Elytron credential store vault | WildFly-specific. Not applicable to Quarkus-based Keycloak. |

---

## Navigation Impact

The new pages integrate seamlessly into the existing documentation structure via `weight` frontmatter ordering:

### Guides Section (weight: 70)
```
10 - Create Instance (existing)
20 - Manage Realms (existing)
25 - Manage Clients (NEW)
30 - Manage Users and Roles (existing, extended)
35 - Configure Identity Providers (NEW)
40 - Monitoring (existing, extended)
50 - Configure Authentication (NEW)
55 - Manage Sessions (NEW)
60 - Configure Authorization (NEW)
```

### How To Section (weight: 80)
```
10 - Configure Ingress and TLS (existing)
20 - Configure LDAP Federation (NEW)
25 - Configure Database (NEW)
30 - Migrate from RH-SSO (existing)
35 - Import and Export (NEW)
40 - Security Hardening (NEW)
50 - Customize Themes (NEW)
```

### Concepts Section (weight: 90)
```
10 - Realm and Multi-Tenancy (existing)
20 - SSO and Authentication Protocols (existing)
30 - Identity Federation (existing)
35 - Account Console (NEW)
40 - Authorization Services (NEW)
50 - Extensibility (NEW)
```

### Integration Section (weight: 95, NEW)
```
10 - Secure Applications with OIDC (NEW)
20 - OIDC Endpoints Reference (NEW)
```

### API Reference Section (weight: 110)
```
Kubernetes APIs (existing)
  - Keycloak CRD (existing)
  - KeycloakRealmImport CRD (existing)
REST API (NEW)
  - Admin REST API (NEW)
```

---

## Verification Checklist

- [ ] Build the documentation site (`yarn build`) to verify no broken links or MDX syntax errors
- [ ] Review all `<Directive type="warning">` blocks for items requiring version-specific verification
- [ ] Confirm the Keycloak base image reference matches Alauda's build pipeline
- [ ] Confirm supported database versions for the compatibility matrix
- [ ] Test navigation ordering matches the expected structure
- [ ] Validate REST API curl examples against a running Keycloak 26.4.7 instance
