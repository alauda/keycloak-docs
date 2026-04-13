# Comparative Gap Analysis: Alauda Build of Keycloak vs Red Hat Single Sign-On 7.6

**Date:** 2026-04-13  
**Scope:** Documentation comparison between the local `keycloak-docs` project (Alauda Build of Keycloak v26.4) and Red Hat Single Sign-On 7.6 documentation (part of Red Hat Application Foundations 2024).

---

## 1. Executive Summary

The Alauda Build of Keycloak documentation currently contains **24 pages (~12,500 words)** organized across 6 sections (Introduction, Concepts, Guides, How-To, API Reference, Release Notes). It is well-structured, Kubernetes-native, and production-oriented.

Red Hat SSO 7.6 documentation contains **10 guides** with significantly deeper coverage across server administration, application integration, authorization services, developer extensibility, and OpenShift deployment.

This report identifies **18 gap areas** organized by priority. Each gap is classified as:

- **P0 (Critical)** — Core functionality documentation that users expect and need for production use
- **P1 (Important)** — Significant feature documentation that differentiates a mature product
- **P2 (Nice-to-have)** — Advanced or niche topics that round out the documentation

---

## 2. Documentation Structure Comparison

| RH-SSO Guide | Local Equivalent | Coverage |
|---|---|---|
| Release Notes | `release_notes.mdx` | Partial (1 version only) |
| Getting Started Guide | `quick_start.mdx` | Good |
| Server Installation and Configuration Guide | `install.mdx`, `architecture.mdx`, `configure_ingress_tls.mdx` | Partial |
| Upgrading Guide | `upgrade.mdx` | Partial |
| **Server Administration Guide** | `concepts/`, `functions/`, `how_to/` (scattered) | **Major gaps** |
| **Securing Applications and Services Guide** | **None** | **Missing entirely** |
| **Authorization Services Guide** | **None** | **Missing entirely** |
| Red Hat SSO for OpenShift | `install.mdx`, `create_instance.mdx` (Kubernetes-focused) | Partial (different platform) |
| **Server Developer Guide** | **None** | **Missing entirely** |
| API Documentation | `apis/` (CRD only) | Partial |

---

## 3. Detailed Gap Analysis

### GAP-01: Authentication Flow Configuration [P0]

**RH-SSO Coverage:** Server Administration Guide Chapter 8 — password policies (hashing algorithms, complexity rules, expiry, blacklist), OTP policies (TOTP/HOTP configuration), authentication flow customization (creating custom flows, passwordless browser login, step-up authentication, user session limits), Kerberos/SPNEGO, X.509 client certificate authentication, WebAuthn (passwordless, loginless, Windows Hello), recovery codes.

**Local Coverage:** `concepts/20-sso_and_protocols.mdx` mentions protocols conceptually. No operational guidance on configuring authentication flows, password policies, OTP, or advanced authentication methods.

**Gap:** Missing documentation for:
- Password policy configuration (complexity, expiry, hashing)
- OTP/MFA setup and policies
- Custom authentication flow creation and management
- WebAuthn / passwordless authentication
- Kerberos / X.509 certificate authentication
- Step-up authentication and conditional flows

**Recommendation:** Create a new guide `functions/50-configure_authentication.mdx` covering password policies, OTP, and authentication flow customization. Focus on Admin Console and REST API configuration methods relevant to the Kubernetes deployment model.

---

### GAP-02: Client Management (OIDC and SAML) [P0]

**RH-SSO Coverage:** Server Administration Guide Chapter 12 — creating OIDC clients (basic/advanced settings, confidential client credentials, client secret rotation, service accounts, audience), creating SAML clients (IDP-initiated login, entity descriptors), client links, protocol mappers (token/assertion mapping, script mapper), client scopes (protocol, consent, linking, evaluating, permissions, defaults), client policies (conditions, executors, profiles).

**Local Coverage:** `functions/20-manage_realms.mdx` mentions clients as part of realm import. `concepts/20-sso_and_protocols.mdx` describes protocols conceptually. No dedicated client management guide.

**Gap:** Missing documentation for:
- Creating and configuring OIDC clients (all settings)
- Creating and configuring SAML clients
- Client secret management and rotation
- Service account configuration
- Protocol mappers and token customization
- Client scopes (creation, linking, evaluation)
- Client policies (profiles, executors, conditions)

**Recommendation:** Create `functions/25-manage_clients.mdx` covering OIDC and SAML client creation and configuration, including protocol mappers and client scopes.

---

### GAP-03: Identity Provider (IdP) Brokering Configuration [P0]

**RH-SSO Coverage:** Server Administration Guide Chapter 9 — brokering overview, default IdP configuration, social IdPs (GitHub, Google, Microsoft, GitLab, etc.), OpenID Connect v1.0 IdPs, SAML v2.0 IdPs, client-suggested IdP, claims/assertions mapping, first login flow, session data, broker logout.

**Local Coverage:** `concepts/30-identity_federation.mdx` explains the concept of identity brokering and lists supported types. No step-by-step guide for configuring any IdP.

**Gap:** Missing documentation for:
- Step-by-step IdP brokering setup (OIDC and SAML)
- Social IdP configuration (GitHub, Google, etc.)
- Claims/assertions mapping from external IdPs
- First login flow customization for brokered users
- Default IdP and client-suggested IdP

**Recommendation:** Create `functions/35-configure_identity_providers.mdx` with step-by-step guides for at least OIDC IdP, SAML IdP, and one social provider (e.g., GitHub). Reference the concepts page for foundational understanding.

---

### GAP-04: User Federation (LDAP/AD) Step-by-Step Configuration [P0]

**RH-SSO Coverage:** Server Administration Guide Chapter 4 — adding user federation providers, LDAP/Active Directory detailed configuration (connection settings, bind DN, search base, storage mode, edit mode, SSL, synchronization schedules, LDAP mappers for attributes/roles/groups, password hashing, troubleshooting), SSSD/FreeIPA integration, custom storage providers.

**Local Coverage:** `concepts/30-identity_federation.mdx` explains LDAP/AD federation conceptually and lists key configuration options. No step-by-step configuration guide.

**Gap:** Missing documentation for:
- Step-by-step LDAP/AD connection and configuration
- LDAP mapper configuration (attribute, role, group mappers)
- Synchronization schedule setup
- SSL/TLS for LDAP connections
- Edit modes and storage modes explained with practical examples
- Troubleshooting LDAP connectivity

**Recommendation:** Create `how_to/20-configure_ldap_federation.mdx` as a step-by-step guide for LDAP/AD integration, including mapper configuration and troubleshooting.

---

### GAP-05: Securing Applications and Services [P1]

**RH-SSO Coverage:** Entire dedicated guide (Guide 6) — Java adapters (JBoss EAP, Spring Boot, Fuse, servlet filter), JavaScript adapter, Node.js adapter, OIDC endpoints reference, SAML adapters, Docker registry authentication, client registration service, token exchange.

**Local Coverage:** None. No documentation on how applications integrate with Keycloak.

**Gap:** Missing entirely:
- How to secure a web application with OIDC
- How to secure a REST API with bearer tokens
- OIDC endpoint reference (authorization, token, userinfo, logout, introspection)
- SAML integration for applications
- Token exchange workflows
- Client registration service

**Recommendation:** Create a new section `integration/` with at least:
- `integration/10-secure_app_with_oidc.mdx` — How to integrate an application using OIDC (generic, not adapter-specific since Keycloak 26.x deprecates most adapters)
- `integration/20-oidc_endpoints_reference.mdx` — OIDC endpoint reference

**Note:** RH-SSO 7.6 documents legacy Java/JS/Node adapters. Keycloak 26.x has deprecated most client-side adapters in favor of standard OIDC libraries. The local docs should document the modern approach (standard OIDC/OAuth2 libraries) rather than copying deprecated adapter documentation.

---

### GAP-06: Authorization Services [P1]

**RH-SSO Coverage:** Entire dedicated guide (Guide 7) — resource servers, resources and scopes, policies (user-based, role-based, JavaScript, time-based, aggregated, client, group, client scope, regex), permissions (resource-based, scope-based), policy evaluation and testing, UMA (User-Managed Access), Protection API, RPT (Requesting Party Token) introspection, policy enforcers.

**Local Coverage:** None. Authorization services are not mentioned.

**Gap:** Missing entirely:
- Authorization services concepts and architecture
- Resource server management
- Policy types and creation
- Permission management
- Policy evaluation and testing
- UMA workflow

**Recommendation:** Create:
- `concepts/40-authorization_services.mdx` — Concept explanation of fine-grained authorization
- `functions/60-configure_authorization.mdx` — Step-by-step guide for setting up authorization services

**Note:** Authorization services in Keycloak 26.x are functionally similar to RH-SSO 7.6 but the Admin Console UI has changed. Screenshots and step-by-step flows should be validated against the actual Keycloak 26.x Admin Console.

---

### GAP-07: Event Auditing and Logging [P1]

**RH-SSO Coverage:** Server Administration Guide Chapter 14 — login event types, admin event types, event listeners (logging, email), configuring event storage and retention.

**Local Coverage:** `functions/40-monitor.mdx` covers Prometheus metrics and health probes. No coverage of Keycloak's built-in event auditing system.

**Gap:** Missing documentation for:
- Login event auditing (types, retention, listeners)
- Admin event auditing
- Email event listeners
- Event log configuration and querying

**Recommendation:** Add an "Event Auditing" section to `functions/40-monitor.mdx` or create a separate `functions/45-configure_auditing.mdx`.

---

### GAP-08: Security Threats and Mitigation [P1]

**RH-SSO Coverage:** Server Administration Guide Chapter 16 — host security, admin endpoint protection, brute force detection, read-only user attributes, clickjacking protection, SSL/HTTPS enforcement, CSRF protection, redirect URI validation, FAPI compliance, compromised token handling, open redirect prevention, password database compromise, token scope limiting, token audience restriction, authentication session security, SQL injection prevention.

**Local Coverage:** `architecture.mdx` mentions TLS and security contexts briefly. `how_to/10-configure_ingress_tls.mdx` covers TLS. No dedicated security hardening guide.

**Gap:** Missing documentation for:
- Brute force detection configuration
- Admin endpoint protection
- Redirect URI security best practices
- CSRF and clickjacking protection
- Token security (scope limiting, audience restriction)
- FAPI compliance configuration
- General security hardening checklist

**Recommendation:** Create `how_to/40-security_hardening.mdx` covering the most impactful security configurations: brute force detection, admin endpoint protection, redirect URI validation, and token security.

---

### GAP-09: Session Management [P1]

**RH-SSO Coverage:** Server Administration Guide Chapter 6 — administering sessions (view active sessions, logout all users, per-app sessions, per-user sessions), revocation policies, session and token timeouts (SSO/idle/max, client session, offline session), offline access configuration, offline session preloading, transient sessions.

**Local Coverage:** `concepts/20-sso_and_protocols.mdx` mentions token lifespans briefly. `functions/20-manage_realms.mdx` lists token lifespan defaults. No dedicated session management guide.

**Gap:** Missing documentation for:
- Viewing and managing active sessions
- Session timeout configuration (SSO idle, SSO max, client session timeouts)
- Offline session configuration
- Revocation policies
- Logout all / session termination procedures

**Recommendation:** Add session management content to `functions/30-manage_users.mdx` or create a dedicated `functions/55-manage_sessions.mdx`.

---

### GAP-10: Themes and Branding Customization [P1]

**RH-SSO Coverage:** Server Developer Guide Chapter 3 — theme types (login, account, admin, email, welcome), configuring themes, creating custom themes (properties, stylesheets, scripts, images, messages/i18n, HTML templates, email templates), deploying themes (archive, module), theme selector, locale selector.

**Local Coverage:** `functions/20-manage_realms.mdx` mentions theme selection as a realm setting (one line in a table). No guide on customizing themes.

**Gap:** Missing documentation for:
- How to create and deploy a custom login page theme
- How to customize email templates
- Theme structure and configuration
- Deploying themes in a Kubernetes/Operator-managed environment (ConfigMap/Volume mount)

**Recommendation:** Create `how_to/50-customize_themes.mdx` focusing on the Kubernetes deployment model (how to mount custom themes via ConfigMap or persistent volume in the Keycloak CR).

**Note:** Theme deployment in Keycloak 26.x (Operator-managed) differs significantly from RH-SSO 7.6 (JBoss-based deployment). The guide should focus on the Kubernetes-native approach, not the legacy file-based deployment.

---

### GAP-11: Admin REST API Reference [P1]

**RH-SSO Coverage:** Server Developer Guide Chapter 2 — Admin REST API overview, authentication methods (username/password, service account), CURL examples for common operations. Guide 10 — Full API documentation reference.

**Local Coverage:** `apis/` section covers only Kubernetes CRDs (Keycloak, KeycloakRealmImport). `functions/30-manage_users.mdx` shows one REST API example for user creation.

**Gap:** Missing documentation for:
- Admin REST API overview and authentication
- Common API operations (realm, user, client, role management)
- API endpoint reference or link to upstream Keycloak API docs

**Recommendation:** Create `apis/rest_api/index.mdx` providing an overview of the Admin REST API, authentication methods, and links to the upstream Keycloak REST API documentation. Do not duplicate the full API reference (it is auto-generated upstream), but provide practical examples for common operations.

---

### GAP-12: Database Configuration and Management [P2]

**RH-SSO Coverage:** Server Installation Guide Chapter 6 — JDBC driver installation, datasource configuration (connection URL, dialect, Unicode settings), supported databases, connection pool tuning.

**Local Coverage:** `quick_start.mdx` shows a development PostgreSQL StatefulSet. `functions/10-create_instance.mdx` references database secret configuration. `architecture.mdx` mentions database HA.

**Gap:** Missing documentation for:
- Supported database matrix (PostgreSQL versions, MySQL support if any)
- Production database configuration best practices
- Connection pool tuning
- Database backup and restore procedures
- Database migration between versions

**Recommendation:** Create `how_to/25-configure_database.mdx` covering supported databases, production configuration, and backup procedures.

**Note:** Keycloak 26.x supports only PostgreSQL by default (unlike RH-SSO 7.6 which supported multiple databases). Confirm the actual database support matrix for Alauda Build of Keycloak before documenting.

---

### GAP-13: Import and Export Procedures [P2]

**RH-SSO Coverage:** Server Administration Guide Chapter 15 — database import/export procedures, partial export, scheduled export.

**Local Coverage:** `functions/20-manage_realms.mdx` covers realm import via CRD. `how_to/30-migrate_from_rhsso.mdx` covers export from RH-SSO. No general import/export guide.

**Gap:** Missing documentation for:
- Full database export procedure
- Partial realm export (from Admin Console)
- Scheduled/automated export
- Import methods beyond CRD (CLI import, startup import)

**Recommendation:** Add import/export content to `how_to/30-migrate_from_rhsso.mdx` or create a separate `how_to/35-import_export.mdx`. Focus on Kubernetes-native approaches (e.g., using Jobs for export).

---

### GAP-14: Admin Console Access Control [P2]

**RH-SSO Coverage:** Server Administration Guide Chapter 11 — master realm access control, global admin roles, realm-specific admin roles, dedicated admin consoles per realm, fine-grained admin permissions (per-client, per-role-mapping, full permission list).

**Local Coverage:** `functions/30-manage_users.mdx` covers basic role management but not admin-specific roles or fine-grained permissions.

**Gap:** Missing documentation for:
- Admin role structure (master realm vs. realm-specific)
- Fine-grained admin permissions
- Delegated administration patterns

**Recommendation:** Add a section on admin access control to `functions/30-manage_users.mdx` or create `functions/32-admin_access_control.mdx`.

---

### GAP-15: Vault Integration for Secrets [P2]

**RH-SSO Coverage:** Server Administration Guide Chapter 13 — Kubernetes/OpenShift files vault, Elytron credential store, key resolvers, vault SPI.

**Local Coverage:** `functions/20-manage_realms.mdx` mentions using Kubernetes Secrets for sensitive values in realm import. No dedicated vault integration guide.

**Gap:** Missing documentation for:
- Kubernetes Secrets vault provider configuration
- Using vault references in realm configuration
- External vault integration (HashiCorp Vault if supported)

**Recommendation:** This is partially covered by the existing Kubernetes Secrets approach. Add a brief section on vault configuration to the realm management or security hardening guide. Low priority since Kubernetes Secrets is the natural approach in the Operator model.

---

### GAP-16: Clustering and Cache Configuration [P2]

**RH-SSO Coverage:** Server Installation Guide Chapter 9 — load balancer setup, sticky sessions, multicast/TCP discovery, secure cluster communication, JDBC_PING. Chapter 10 — Infinispan cache configuration, cache types, eviction policies.

**Local Coverage:** `architecture.mdx` describes HA architecture. `functions/10-create_instance.mdx` shows multi-replica configuration.

**Gap:** Missing documentation for:
- Cache configuration tuning
- Distributed cache behavior explanation
- Session replication troubleshooting
- Cross-datacenter replication (if supported)

**Recommendation:** Add cache/clustering details to `architecture.mdx` or create `how_to/60-clustering_and_cache.mdx`. Note that Keycloak 26.x uses a different cache architecture (embedded Infinispan with Kubernetes-native discovery) than RH-SSO 7.6 (JGroups-based). Content must be validated against the actual Keycloak 26.x behavior.

---

### GAP-17: Server Developer Guide (SPI / Extensions) [P2]

**RH-SSO Coverage:** Server Developer Guide — Admin REST API usage, theme development, custom user attributes, identity brokering APIs, Service Provider Interfaces (SPI), User Storage SPI, Vault SPI.

**Local Coverage:** None. No developer extensibility documentation.

**Gap:** Missing documentation for:
- SPI overview and extension points
- Custom authenticator development
- Custom user storage provider development
- Custom protocol mapper development
- Extension deployment in Kubernetes (custom image builds, init containers)

**Recommendation:** Create `concepts/50-extensibility.mdx` providing an overview of Keycloak's extension model and how to deploy custom extensions in the Operator-managed environment. Do not replicate the full SPI reference (defer to upstream Keycloak docs), but document the deployment model specific to Alauda Build of Keycloak.

**Note:** This is an advanced topic. The deployment mechanism for custom extensions in Keycloak 26.x (Quarkus-based) differs fundamentally from RH-SSO 7.6 (WildFly-based). Custom providers must be built into the Keycloak image or loaded via specific mechanisms. This requires validation before documenting.

---

### GAP-18: Account Console End-User Guide [P2]

**RH-SSO Coverage:** Server Administration Guide Chapter 17 — accessing the account console, managing sign-in methods (OTP, WebAuthn, passwordless), device activity, linked identity provider accounts.

**Local Coverage:** None. No end-user focused documentation.

**Gap:** Missing documentation for:
- End-user account console overview
- Self-service password management
- Self-service MFA enrollment
- Device/session management from user perspective

**Recommendation:** Low priority for an operator-focused documentation set. Consider adding a brief `concepts/35-account_console.mdx` overview if end-user self-service is a common use case for Alauda customers.

---

## 4. Summary Matrix

| Gap ID | Topic | Priority | Action | Effort |
|--------|-------|----------|--------|--------|
| GAP-01 | Authentication Flow Configuration | P0 | New page | High |
| GAP-02 | Client Management (OIDC/SAML) | P0 | New page | High |
| GAP-03 | Identity Provider Brokering | P0 | New page | Medium |
| GAP-04 | LDAP/AD Federation Step-by-Step | P0 | New page | Medium |
| GAP-05 | Securing Applications | P1 | New section | High |
| GAP-06 | Authorization Services | P1 | New pages | High |
| GAP-07 | Event Auditing | P1 | Extend or new page | Low |
| GAP-08 | Security Hardening | P1 | New page | Medium |
| GAP-09 | Session Management | P1 | Extend or new page | Low |
| GAP-10 | Themes and Branding | P1 | New page | Medium |
| GAP-11 | Admin REST API Reference | P1 | New page | Medium |
| GAP-12 | Database Configuration | P2 | New page | Low |
| GAP-13 | Import/Export Procedures | P2 | Extend existing | Low |
| GAP-14 | Admin Console Access Control | P2 | Extend existing | Low |
| GAP-15 | Vault Integration | P2 | Extend existing | Low |
| GAP-16 | Clustering and Cache | P2 | Extend existing | Medium |
| GAP-17 | Developer Extensibility (SPI) | P2 | New page | High |
| GAP-18 | Account Console Guide | P2 | New page | Low |

---

## 5. Items That Cannot Be Directly Mapped

The following RH-SSO documentation topics do **not** have direct equivalents in Keycloak 26.x or are fundamentally different:

1. **WildFly/JBoss-based installation** (standalone, domain mode, CLI subsystem configuration) — Keycloak 26.x is Quarkus-based; these installation modes do not exist. No action needed.

2. **Client adapters (Java EAP, Fuse, servlet filter, JavaScript, Node.js)** — Most adapters are deprecated in Keycloak 26.x. Applications should use standard OIDC/OAuth2 libraries. Documentation should reflect the modern approach.

3. **OpenShift Templates and Image Streams** — RH-SSO for OpenShift uses templates. Alauda Build uses a Kubernetes Operator. No action needed; the Operator approach is already documented.

4. **SSSD/FreeIPA integration** — This is specific to Red Hat's identity management stack. Relevance to Alauda customers should be confirmed before documenting.

5. **RPM-based installation** — Not applicable to containerized/Operator deployment.

6. **mod_auth_mellon (Apache HTTPD SAML module)** — Legacy integration approach. Not relevant for modern deployments.

---

## 6. Conclusion

The Alauda Build of Keycloak documentation has a solid foundation with excellent Kubernetes-native content. The primary gaps are in **Keycloak administration features** (authentication flows, client management, IdP brokering, LDAP configuration) and **application integration guidance** (securing apps, authorization services).

Addressing the 4 P0 gaps would bring the documentation to a minimum viable state for production users. Adding the P1 items would achieve rough parity with RH-SSO documentation for the most commonly needed topics.

P2 items are recommended for a mature documentation set but are not blockers for production adoption.
