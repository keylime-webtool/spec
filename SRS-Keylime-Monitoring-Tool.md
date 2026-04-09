# Software Requirements Specification: Keylime Monitoring Dashboard

* **Source Document:** `slides/20260226-Keylime-Monitoring-Tool`
* **Extraction Date:** 2026-04-09
* **Methodology:** Spec-Driven Development (SDD)
* **RFC 2119 Keywords:** MUST, MUST NOT, SHALL, SHALL NOT, SHOULD, SHOULD NOT, RECOMMENDED, MAY, OPTIONAL

---

## 1. Core Objective

The Keylime Monitoring Dashboard (the "System") is a web-based security operations platform that provides centralized monitoring, management, and compliance capabilities for Keylime remote attestation infrastructure. It consumes Keylime's existing Verifier and Registrar REST APIs (v2 pull-mode and v3 push-mode) via mTLS without requiring any modification to Keylime components. The System targets Security Operations (SecOps) teams, System Administrators, Compliance Officers, and DevSecOps engineers.

The System transforms Keylime from a CLI-driven security tool into a visual operations platform, reducing mean time to detect (MTTD) attestation failures from hours to seconds, centralizing policy and certificate lifecycle management, and providing tamper-evident audit trails for compliance reporting.

**Technical Architecture:** React.js + TypeScript SPA frontend, Rust (Axum) async backend, TimescaleDB for time-series storage, Redis for caching, mTLS + rustls for Keylime API communication.

---

## 2. Requirements Inventory Table

### 2.1 Functional Requirements

| Req ID | Description | RFC Level | Traceability Source |
|--------|-------------|-----------|---------------------|
| FR-001 | Fleet overview KPI dashboard | MUST | Dashboard - Key Performance Indicators |
| FR-002 | KPI data auto-refresh with configurable interval | MUST | Dashboard - Key Performance Indicators |
| FR-003 | Sidebar navigation with core modules | MUST | Dashboard - Navigation Structure |
| FR-004 | Global agent search by UUID, IP, or hostname | MUST | Dashboard - Navigation Structure |
| FR-005 | Time range selector for data filtering | MUST | Dashboard - Navigation Structure |
| FR-006 | Auto-refresh toggle for live updates | MUST | Dashboard - Navigation Structure |
| FR-007 | CSV/JSON data export for compliance reporting | MUST | Dashboard - Navigation Structure |
| FR-008 | Dark/Light mode theme preference | MAY | Dashboard - Navigation Structure |
| FR-009 | In-app notification system with badge count | MUST | Dashboard - Navigation Structure |
| FR-010 | Email/Slack integration for critical alerts | SHOULD | Dashboard - Navigation Structure |
| FR-011 | Configurable alert severity thresholds | MUST | Dashboard - Key Performance Indicators |
| FR-012 | Agent fleet list view with sortable columns | MUST | Agent Fleet - List View |
| FR-013 | Agent list pagination | MUST | Agent Fleet - List View |
| FR-014 | Advanced multi-criteria agent filtering | MUST | Agent Fleet - Filtering and Search |
| FR-015 | IP address filtering with CIDR range support | MUST | Agent Fleet - Filtering and Search |
| FR-016 | Bulk operations on selected agents | MUST | Agent Fleet - Filtering and Search |
| FR-017 | Topology/map view for agent distribution | MAY | Agent Fleet - Map and Topology View |
| FR-018 | Agent detail view with info cards | MUST | Agent Detail - Overview Panel |
| FR-019 | Agent actions (Reactivate, Stop, Delete, Force Attest) | MUST | Agent Detail - Overview Panel |
| FR-020 | Agent detail six-tab deep-dive | MUST | Agent Detail - Tabs and Sub-Views |
| FR-021 | PCR values monitoring with diff and expected vs actual | MUST | Agent Detail - PCR Values View |
| FR-022 | PCR change detection with acknowledge/investigate | MUST | Agent Detail - PCR Values View |
| FR-023 | Cross-tab navigation (PCR→IMA, cert→Certificates) | SHOULD | Agent Detail - Tabs and Sub-Views (2/2) |
| FR-024 | Attestation analytics overview (volume, failures, latency) | MUST | Attestation Analytics - Overview Dashboard |
| FR-025 | Failure categorization by type and severity | MUST | Attestation Analytics - Failure Analysis |
| FR-026 | Automatic failure correlation across agents | MUST | Attestation Analytics - Automatic Correlation |
| FR-027 | Incident grouping with suggested root cause | MUST | Attestation Analytics - Actionable Insights |
| FR-028 | One-click policy rollback from incident view | SHOULD | Attestation Analytics - Actionable Insights |
| FR-029 | Push mode (v3 API) attestation analytics | MUST | Attestation Analytics - Push Mode |
| FR-030 | Verification pipeline stage visualization | MUST | Verification Pipeline - Flow Visualization |
| FR-031 | Per-stage verification timing and metrics | MUST | Verification Pipeline - Stage Metrics |
| FR-032 | IMA quote progress tracking with gap detection | MUST | Verification Pipeline - Quote Progress Tracking |
| FR-033 | IMA policy list view and management | MUST | Policy Management - IMA Policies |
| FR-034 | Policy CRUD with inline editor and syntax highlighting | MUST | Policy Management - Policy Editor |
| FR-035 | Policy versioning with change history and rollback | MUST | Policy Management - Policy Editor |
| FR-036 | Measured boot policy management | MUST | Policy Management - Policy Editor |
| FR-037 | Policy assignment matrix | MUST | Policy Management - Policy Editor |
| FR-038 | Pre-update policy impact analysis | MUST | Policy Management - Impact Analysis |
| FR-039 | Two-person rule for policy changes | MUST | Policy Management - Two-Person Rule |
| FR-040 | Time-limited approval window for policy changes | MUST | Policy Management - Two-Person Rule |
| FR-041 | Change management integration (ServiceNow, Jira) | MAY | Policy Management - Two-Person Rule |
| FR-042 | Security audit event log with filtering | MUST | Security Audit - Event Log Dashboard |
| FR-043 | Authorization tracking by action and identity type | MUST | Security Audit - Authorization Tracking |
| FR-044 | Identity verification event monitoring | MUST | Security Audit - Identity Verification Events |
| FR-045 | Anomaly detection on authorization patterns | SHOULD | Security Audit - Authorization Tracking |
| FR-046 | Revocation notification channel monitoring | MUST | Revocation - Notification Channels |
| FR-047 | Alert management dashboard with lifecycle workflow | MUST | Revocation - Alert Workflow |
| FR-048 | Alert auto-escalation after SLA timeout | SHOULD | Revocation - Alert Workflow |
| FR-049 | Alert auto-resolve on successful re-attestation | SHOULD | Revocation - Alert Workflow |
| FR-050 | Unified certificate view across all cert types | MUST | Certificate Management - Overview |
| FR-051 | Certificate expiry dashboard with tiered warnings | MUST | Certificate Management - Expiry Dashboard |
| FR-052 | Certificate detail inspection and chain visualization | MUST | Certificate Management - Operations |
| FR-053 | Automated certificate renewal workflow | SHOULD | Certificate Management - Operations |
| FR-054 | Pull mode attestation monitoring | MUST | Attestation Modes - Pull Mode Monitoring |
| FR-055 | Push mode attestation monitoring | MUST | Attestation Modes - Push Mode Monitoring |
| FR-056 | Mixed mode unified views | MUST | Attestation Modes - Comparative View |
| FR-057 | Backend connectivity status dashboard | MUST | Integration Status - Backend Connectivity |
| FR-058 | Durable attestation backend monitoring | MUST | Integration Status - Durable Attestation |
| FR-059 | Compliance framework mapping reports | MUST | Compliance - Framework Mapping |
| FR-060 | One-click compliance report export (PDF/CSV) | MUST | Compliance - Framework Mapping |
| FR-061 | Tamper-evident hash-chained audit logging | MUST | Compliance - Tamper-Evident Audit Logging |
| FR-062 | Incident response ticketing integration | SHOULD | Incident Response - Integration |
| FR-063 | SIEM integration (Syslog, Splunk HEC, ECS, Prometheus, OpenTelemetry) | MUST | Incident Response - Integration |
| FR-064 | Verifier cluster performance monitoring | MUST | System Performance - Verifier Metrics |
| FR-065 | Database connection pool monitoring | MUST | System Performance - Verifier Metrics |
| FR-066 | API response time tracking (p50/p95/p99) | MUST | System Performance - Verifier Metrics |
| FR-067 | Live configuration view with drift detection | MUST | System Performance - Configuration Monitoring |
| FR-068 | Capacity planning projections | SHOULD | System Performance - Key Metrics |
| FR-069 | Agent state machine visualization (pull + push) | MUST | Keylime - Agent State Machine / Push Mode |
| FR-070 | API version distribution visualization | MUST | Integration Status - Backend Connectivity |

### 2.2 Non-Functional Requirements

| Req ID | Description | RFC Level | Traceability Source |
|--------|-------------|-----------|---------------------|
| NFR-001 | KPI data refresh within 30 seconds | MUST | Dashboard - Key Performance Indicators |
| NFR-002 | Support Keylime API v2 and v3 simultaneously | MUST | Keylime - Data Model Overview |
| NFR-003 | API-first, non-invasive architecture (zero Keylime modifications) | MUST | Technical Architecture - System Design |
| NFR-004 | React.js + TypeScript SPA frontend | MUST | Technical Architecture - Data Flow |
| NFR-005 | Rust (Axum) async backend | MUST | Technical Architecture - Why Rust |
| NFR-006 | Event-driven ingestion as primary data path | MUST | Technical Architecture - Event-Driven Ingestion |
| NFR-007 | Polling fallback with adaptive backpressure | MUST | Technical Architecture - Event-Driven Ingestion |
| NFR-008 | Scale to 100K+ agents under event-driven mode | SHOULD | Scalability - Ingestion Model Comparison |
| NFR-009 | Scale to ~1,000 agents under polling fallback | MUST | Scalability - Ingestion Model Comparison |
| NFR-010 | Active/Passive HA with <30s RTO and 0 RPO | MUST | High Availability - Architecture |
| NFR-011 | Active/Active HA for 5K+ agents | SHOULD | High Availability - Architecture |
| NFR-012 | Air-gapped deployment with no external dependencies | MUST | Deployment - Offline & Air-Gapped |
| NFR-013 | Self-contained packaging (no CDN, single binary) | MUST | Deployment - Offline & Air-Gapped |
| NFR-014 | WCAG 2.1 Level AA accessibility compliance | MUST | Deployment - Offline & Air-Gapped |
| NFR-015 | Container (OCI), Kubernetes (Helm), RPM, systemd deployment options | MUST | Technical Architecture - Deployment |
| NFR-016 | Graceful degradation when components are unavailable | MUST | High Availability - Architecture |
| NFR-017 | Circuit breaker on Verifier API latency | MUST | Technical Architecture - Event-Driven Ingestion |
| NFR-018 | Per-user and global request rate limiting | MUST | Technical Architecture - IMA Log & Data Decoupling |
| NFR-019 | Cache TTLs: agent list 10s, detail 30s, policies 60s, certs 300s | MUST | Scalability - Cache Invalidation |
| NFR-020 | Periodic reconciliation sweep every 5 minutes | MUST | Technical Architecture - Event-Driven Ingestion |
| NFR-021 | WebSocket real-time updates for UI | MUST | Technical Architecture - Data Flow |
| NFR-022 | Signed update packages with SBOM for offline updates | MUST | Deployment - Offline & Air-Gapped |
| NFR-023 | Maximum 5 parallel concurrent log fetches to Verifier | MUST | Technical Architecture - IMA Log & Data Decoupling |

### 2.3 Security Requirements

| Req ID | Description | RFC Level | Traceability Source |
|--------|-------------|-----------|---------------------|
| SR-001 | OIDC/SAML identity provider authentication | MUST | Dashboard Authentication - User Identity |
| SR-002 | MFA mandatory for Admin role | MUST | Dashboard Authentication - User Identity |
| SR-003 | Three-tier RBAC (Viewer, Operator, Admin) | MUST | Dashboard RBAC - Role Definitions |
| SR-004 | mTLS for all Keylime API communication | MUST | Threat Model - Trust Boundaries |
| SR-005 | mTLS private key MUST NEVER be stored on disk in cleartext | MUST | Secret Management - Credential Lifecycle |
| SR-006 | HSM or Vault-backed private key storage | MUST | Secret Management - Credential Lifecycle |
| SR-007 | TLS encryption on all network connections (no cleartext paths) | MUST | Transport Security - Encrypted Data Paths |
| SR-008 | Browser→API: TLS 1.3 minimum | MUST | Transport Security - Encrypted Data Paths |
| SR-009 | API→Keylime: TLS 1.2+ minimum | MUST | Transport Security - Encrypted Data Paths |
| SR-010 | Short-lived JWT session tokens (15 min) with refresh rotation | MUST | Dashboard Authentication - User Identity |
| SR-011 | Server-side session revocation | MUST | Dashboard Authentication - User Identity |
| SR-012 | CSP headers and input sanitization (XSS/injection prevention) | MUST | Threat Model - Threat Catalog |
| SR-013 | Never cache or store raw TPM quotes, IMA logs, boot logs | MUST | Threat Model - Data Classification |
| SR-014 | Never display or cache raw PoP tokens | MUST | Attestation Modes - Comparative View |
| SR-015 | Tamper-evident hash-chained audit log with RFC 3161 anchoring | MUST | Compliance - Tamper-Evident Audit Logging |
| SR-016 | SSRF protection on webhook URLs (allowlist, block RFC 1918) | MUST | Revocation - Alert Workflow |
| SR-017 | Two-person approval for policy changes (N-of-M quorum) | MUST | Policy Management - Two-Person Rule |
| SR-018 | Approver cannot be the same as drafter | MUST | Policy Management - Two-Person Rule |
| SR-019 | Multi-tenancy isolation (cross-tenant data never mixed) | MUST | Dashboard RBAC - Multi-Tenancy |
| SR-020 | Data classification enforcement (SECRET, CONFIDENTIAL, INTERNAL) | MUST | Threat Model - Data Classification |
| SR-021 | Write operations blocked at proxy for non-Admin roles | MUST | Dashboard RBAC - Role Definitions |
| SR-022 | mTLS sidecar option (Envoy/Ghostunnel) | MAY | Transport Security - mTLS Sidecar Option |
| SR-023 | `#![forbid(unsafe_code)]` on dashboard Rust crate | MUST | Technical Architecture - Why Rust |
| SR-024 | Signed cache entries with TTLs to mitigate cache poisoning | MUST | Threat Model - Threat Catalog |
| SR-025 | Identity alert on TPM key change during re-registration | MUST | Security Audit - Identity Verification Events |
| SR-026 | Audit log minimum retention of 1 year for compliance | MUST | Compliance - Tamper-Evident Audit Logging |
| SR-027 | Emergency bypass with break-glass audit trail | MUST | Policy Management - Two-Person Rule |
| SR-028 | Configurable idle session timeout | MUST | Dashboard Authentication - User Identity |
| SR-029 | Rate limiting on dashboard session creation endpoint | MUST | Attestation Modes - Comparative View |

---

## 3. Detailed Functional Requirements

### FR-001: Fleet Overview KPI Dashboard

**Description:** The System MUST display a fleet overview dashboard presenting computed KPIs derived from the Keylime Verifier and Registrar APIs. The dashboard MUST show: Total Active Agents, Failed Agents (states 7, 9, 10), Attestation Success Rate, Average Attestation Latency, Certificate Expiry Warnings, Active IMA Policies, Revocation Events (24h), Consecutive Failures per agent, and Registration Count.

**Trace:** Dashboard - Key Performance Indicators; Dashboard - Main Screen Layout

```gherkin
Feature: Fleet Overview KPI Dashboard

  Scenario: Display fleet health KPIs
    Given the dashboard backend is connected to the Verifier API via mTLS
    And the Verifier reports 247 agents in GET_QUOTE state
    And 3 agents are in FAILED state
    When the user navigates to the Fleet Overview Dashboard
    Then the dashboard MUST display "247" as Active Agents
    And the dashboard MUST display "3" as Failed Agents
    And the Attestation Success Rate MUST be computed from attestation history
    And Certificate Expiry Warnings MUST reflect certificates expiring within 30 days

  Scenario: Failed agent threshold alert
    Given the alert threshold for Failed Agents is configured to "any count > 0"
    When 1 or more agents enter state 7 (FAILED), 9 (INVALID_QUOTE), or 10 (TENANT_FAILED)
    Then the Failed Agents KPI MUST display in critical color
    And an alert MUST be raised in the notification system
```

### FR-002: KPI Data Auto-Refresh

**Description:** The System MUST refresh KPI data at a configurable interval. The default refresh interval MUST be 30 seconds. The System MUST support both HTTP polling and WebSocket push as refresh mechanisms.

**Trace:** Dashboard - Key Performance Indicators

```gherkin
Feature: KPI Data Auto-Refresh

  Scenario: Default KPI refresh interval
    Given the auto-refresh toggle is enabled
    And the refresh interval is set to the default of 30 seconds
    When 30 seconds elapse
    Then the System MUST fetch updated KPI data from the Verifier API
    And the dashboard MUST re-render all KPI values

  Scenario: KPI refresh via WebSocket
    Given the backend supports WebSocket push
    And a WebSocket connection is established
    When the backend receives an agent state change event
    Then the updated KPI data MUST be pushed to the browser within 1 second
```

### FR-003: Sidebar Navigation

**Description:** The System MUST provide a persistent sidebar navigation with the following modules: Dashboard (Fleet overview), Agents (Fleet management), Attestations (Analytics), Policies (IMA & MB), Certificates (TLS/TPM certs), Revocations (Alert history), Performance (System metrics), Audit Log (Security events), Integrations (Backend status), and Settings (Configuration).

**Trace:** Dashboard - Navigation Structure

```gherkin
Feature: Sidebar Navigation

  Scenario: Navigate between core modules
    Given the user is authenticated and viewing the dashboard
    When the user clicks "Agents" in the sidebar
    Then the Agent Fleet Management view MUST be displayed
    And the sidebar MUST highlight the "Agents" entry as active

  Scenario: All core modules accessible
    Given the user is authenticated
    Then the sidebar MUST display navigation entries for all 10 core modules
    And each entry MUST route to its corresponding view
```

### FR-004: Global Agent Search

**Description:** The System MUST provide a global search bar that allows searching agents by UUID (exact or partial match), IP address (including CIDR range support), or hostname.

**Trace:** Dashboard - Navigation Structure; Agent Fleet - Filtering and Search

```gherkin
Feature: Global Agent Search

  Scenario: Search agent by partial UUID
    Given the agent fleet contains an agent with UUID "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
    When the user types "a1b2c3" in the global search bar
    Then the search results MUST include agent "a1b2c3d4-e5f6-7890-abcd-ef1234567890"

  Scenario: Search agent by CIDR range
    Given the fleet contains agents at IPs 192.168.1.10, 192.168.1.11, 192.168.2.10
    When the user searches for "192.168.1.0/24"
    Then the search results MUST include agents at 192.168.1.10 and 192.168.1.11
    And the results MUST NOT include the agent at 192.168.2.10
```

### FR-005: Time Range Selector

**Description:** The System MUST provide a time range selector allowing users to filter displayed data by predefined intervals: 1h, 6h, 24h, 7d, 30d, and a custom date range picker.

**Trace:** Dashboard - Navigation Structure

```gherkin
Feature: Time Range Selector

  Scenario: Filter attestation data by time range
    Given the user is viewing the Attestation Analytics view
    When the user selects the "24h" time range
    Then only attestation data from the last 24 hours MUST be displayed
    And charts and tables MUST update to reflect the selected range

  Scenario: Custom date range
    Given the user selects "Custom" in the time range selector
    When the user specifies a start date of "2026-02-20" and end date of "2026-02-25"
    Then only data within that date range MUST be displayed
```

### FR-006: Auto-Refresh Toggle

**Description:** The System MUST provide a toggle control that enables or disables live automatic updates of dashboard data.

**Trace:** Dashboard - Navigation Structure

```gherkin
Feature: Auto-Refresh Toggle

  Scenario: Disable auto-refresh
    Given auto-refresh is currently enabled
    When the user toggles auto-refresh off
    Then dashboard data MUST stop refreshing automatically
    And the displayed data MUST remain static until manually refreshed or re-enabled

  Scenario: Enable auto-refresh
    Given auto-refresh is currently disabled
    When the user toggles auto-refresh on
    Then dashboard data MUST begin refreshing at the configured interval
```

### FR-007: Data Export

**Description:** The System MUST provide CSV and JSON export functionality for compliance reporting. Export MUST be available for agent fleet data, attestation analytics, audit logs, and certificate data.

**Trace:** Dashboard - Navigation Structure

```gherkin
Feature: Data Export

  Scenario: Export agent fleet data as CSV
    Given the user has the Operator or Admin role
    And the agent fleet list is displayed with active filters
    When the user clicks "Export" and selects "CSV"
    Then the System MUST generate a CSV file containing the filtered agent data
    And the file MUST be downloaded to the user's browser

  Scenario: Viewer cannot export
    Given the user has the Viewer role
    When the user looks for the Export button
    Then the Export button MUST be disabled or hidden
```

### FR-008: Dark/Light Mode

**Description:** The System MAY provide a theme preference toggle allowing users to switch between dark and light visual modes.

**Trace:** Dashboard - Navigation Structure

```gherkin
Feature: Dark/Light Mode

  Scenario: Switch to dark mode
    Given the user is viewing the dashboard in light mode
    When the user selects "Dark Mode" in the theme settings
    Then the entire dashboard UI MUST render with a dark color scheme
    And the preference MUST persist across sessions
```

### FR-009: In-App Notification System

**Description:** The System MUST provide an in-app notification bell displaying an unread notification badge count. Notifications MUST include attestation failures, certificate expiry warnings, policy updates, agent registration events, and revocation events. Severity thresholds for generating notifications MUST be configurable.

**Trace:** Dashboard - Navigation Structure

```gherkin
Feature: In-App Notification System

  Scenario: Display notification badge count
    Given there are 3 unread critical notifications and 2 unread warnings
    When the user views the dashboard header
    Then the notification bell MUST display a badge count of 5

  Scenario: Mark notification as read
    Given the user has unread notifications
    When the user opens the notification panel and clicks a notification
    Then the notification MUST be marked as read
    And the badge count MUST decrement by 1
```

### FR-010: External Alert Integration

**Description:** The System SHOULD support sending critical alert notifications to external channels including Email and Slack. Alert routing MUST be configurable by severity threshold.

**Trace:** Dashboard - Navigation Structure

```gherkin
Feature: External Alert Integration

  Scenario: Send critical alert to Slack
    Given Slack webhook integration is configured
    And the alert severity threshold for Slack is set to "CRITICAL"
    When a CRITICAL attestation failure is detected
    Then the System SHOULD send a notification to the configured Slack channel
    And the notification MUST include the agent ID, failure type, and timestamp
```

### FR-011: Configurable Alert Thresholds

**Description:** The System MUST allow administrators to configure alert thresholds per deployment. Configurable thresholds MUST include: attestation success rate floor (default: 99%), average latency ceiling (default: 2x quote_interval), certificate expiry warning window (default: 30 days), consecutive failure count (default: 3), and revocation event count.

**Trace:** Dashboard - Key Performance Indicators

```gherkin
Feature: Configurable Alert Thresholds

  Scenario: Configure attestation rate threshold
    Given the user has the Admin role
    When the admin sets the attestation success rate threshold to 98%
    Then the System MUST raise an alert only when the rate falls below 98%
    And the previous threshold of 99% MUST no longer trigger alerts

  Scenario: Default thresholds applied
    Given no custom thresholds are configured
    When the attestation success rate falls below 99%
    Then the System MUST raise an alert using the default threshold
```

### FR-012: Agent Fleet List View

**Description:** The System MUST display agents in a sortable, paginated table with columns: Agent ID, IP Address, Operational State, Last Attestation time, Assigned Policy, Failure Count, and Actions. Rows for agents in FAILED or INVALID_QUOTE states MUST be visually highlighted. Rows for agents in RETRY state MUST be highlighted with a warning indicator.

**Trace:** Agent Fleet - List View

```gherkin
Feature: Agent Fleet List View

  Scenario: Display agent list with state-based highlighting
    Given the Verifier reports agents in various states
    When the user navigates to the Agent Fleet view
    Then agents in GET_QUOTE state MUST display with a success indicator
    And agents in FAILED state MUST display with a critical/danger indicator
    And agents in RETRY state MUST display with a warning indicator
    And each row MUST show Agent ID, IP, State, Last Attest, Policy, Failures, and Actions

  Scenario: Paginated agent list
    Given the Verifier manages 250 agents
    And the page size is set to 25
    When the user views the agent fleet list
    Then the display MUST show "Showing 1-25 of 250 agents"
    And pagination controls MUST allow navigating to page 2 through 10
```

### FR-013: Agent List Pagination

**Description:** The System MUST paginate the agent fleet list. The System MUST display the current page range, total agent count, and total page count. Page size SHOULD be configurable.

**Trace:** Agent Fleet - List View

```gherkin
Feature: Agent List Pagination

  Scenario: Navigate to next page
    Given the agent fleet contains 250 agents with a page size of 25
    And the user is viewing page 1
    When the user clicks "Next Page"
    Then agents 26-50 MUST be displayed
    And the footer MUST show "Showing 26-50 of 250 agents | Page 2 of 10"
```

### FR-014: Advanced Multi-Criteria Agent Filtering

**Description:** The System MUST allow filtering the agent fleet by multiple criteria simultaneously: Agent UUID (exact or partial), IP Address (CIDR range), Operational State (multi-select), Verifier Assignment, IMA Policy, MB Policy, Last Attestation time range, Failure Count (min/max), Registration Date range, and API Version.

**Trace:** Agent Fleet - Filtering and Search

```gherkin
Feature: Advanced Agent Filtering

  Scenario: Filter by state and policy simultaneously
    Given the fleet contains agents in various states with different policies
    When the user selects state filter "FAILED" and policy filter "production-v2"
    Then only agents in FAILED state assigned to "production-v2" MUST be displayed
    And agents in other states or with other policies MUST be hidden

  Scenario: Filter by failure count threshold
    Given the fleet contains agents with failure counts 0, 1, 3, 5
    When the user sets the failure count minimum to 3
    Then only agents with 3 or more failures MUST be displayed
```

### FR-015: IP Address CIDR Filtering

**Description:** The System MUST support filtering agents by IP address using CIDR notation (e.g., 192.168.1.0/24) in addition to exact IP match.

**Trace:** Agent Fleet - Filtering and Search

```gherkin
Feature: CIDR IP Filtering

  Scenario: Filter agents by subnet
    Given the fleet contains agents at IPs 10.0.1.5, 10.0.1.20, 10.0.2.15
    When the user enters IP filter "10.0.1.0/24"
    Then agents at 10.0.1.5 and 10.0.1.20 MUST be displayed
    And the agent at 10.0.2.15 MUST NOT be displayed
```

### FR-016: Bulk Operations

**Description:** The System MUST support selecting multiple agents via checkboxes and performing bulk operations: Reactivate (resume monitoring), Stop (pause monitoring), Delete (remove from verifier), Reassign Policy (change IMA/MB policy), and Export (download agent data as CSV).

**Trace:** Agent Fleet - Filtering and Search

```gherkin
Feature: Bulk Agent Operations

  Scenario: Bulk reactivate stopped agents
    Given the user has selected 5 agents that are in a stopped state
    And the user has the Operator or Admin role
    When the user clicks "Reactivate"
    Then the System MUST send reactivation requests for all 5 agents to the Verifier API
    And each agent's state MUST update upon successful reactivation

  Scenario: Bulk operations denied for Viewer role
    Given the user has the Viewer role
    When the user selects agents in the fleet list
    Then the Reactivate, Stop, Delete, and Reassign Policy buttons MUST be disabled
```

### FR-017: Topology/Map View

**Description:** The System MAY provide an optional topology/map view visualizing agent distribution across infrastructure. Agents MUST be groupable by datacenter, rack, or subnet. The view MUST show verifier-to-agent assignment mapping and registrar connection status. Interactive features MUST include click-to-drill-down on datacenters, hover for agent summary, and color-coded health indicators.

**Trace:** Agent Fleet - Map and Topology View

```gherkin
Feature: Topology View

  Scenario: Drill down into datacenter
    Given the topology view shows 3 datacenters: DC-East (120 agents), DC-West (85 agents), DC-EU (45 agents)
    When the user clicks on "DC-East"
    Then the view MUST expand to show individual agents within DC-East
    And failed agents MUST be indicated with a danger color

  Scenario: Hover for agent summary
    Given the topology view is displayed
    When the user hovers over an agent node
    Then a tooltip MUST display the agent UUID, IP, state, and last attestation time
```

### FR-018: Agent Detail View

**Description:** The System MUST display an agent detail page containing: Agent Information card (UUID, IP, Verifier assignment, Registration date), Attestation Statistics card (total attestations, last successful, consecutive failures, boot time), Cryptographic Details card (hash algorithm, encryption, signing, IMA PCRs), and Policy Assignment card (IMA policy, MB policy, TPM policy with a "Change Policy" action).

**Trace:** Agent Detail - Overview Panel

```gherkin
Feature: Agent Detail View

  Scenario: Display agent information
    Given agent "a1b2c3d4-e5f6-7890" exists in the Verifier
    When the user navigates to the agent detail page
    Then the Agent Information card MUST display UUID, IP address, assigned Verifier, and registration date
    And the Attestation Statistics card MUST display total attestations, last successful time, and consecutive failures
    And the Cryptographic Details card MUST display hash algorithm, encryption, and signing algorithms
    And the Policy Assignment card MUST display assigned IMA and MB policies
```

### FR-019: Agent Actions

**Description:** The System MUST provide the following actions on an individual agent detail page: Reactivate (resume monitoring), Stop (pause monitoring), Delete (remove from verifier), Force Attest (trigger immediate attestation), View Quotes (display raw TPM quote data), and Export (download agent data). Actions MUST be restricted by RBAC role.

**Trace:** Agent Detail - Overview Panel

```gherkin
Feature: Agent Detail Actions

  Scenario: Force attestation on an agent
    Given the user has the Operator or Admin role
    And the user is viewing agent detail for "a1b2c3d4"
    When the user clicks "Force Attest"
    Then the System MUST trigger an immediate attestation cycle for agent "a1b2c3d4"
    And the attestation result MUST update on the Timeline tab

  Scenario: Delete agent requires Admin role
    Given the user has the Operator role
    When the user views the agent detail page
    Then the "Delete" action button MUST be disabled or hidden
```

### FR-020: Agent Detail Six-Tab Deep-Dive

**Description:** The System MUST provide six specialized tabs on the agent detail page: (1) Timeline — attestation success/failure history with zoomable time range; (2) PCR Values — current PCR bank values with change history and diffs; (3) IMA Log — measurement list entries with policy match/mismatch indicators and search by file path or hash; (4) Boot Log — UEFI event log entries with measured boot validation; (5) Certificates — EK, AK, IAK, IDevID, mTLS certificate details and expiry countdown; (6) Raw Data — JSON view of full agent record with copy/export.

**Trace:** Agent Detail - Tabs and Sub-Views

```gherkin
Feature: Agent Detail Tabs

  Scenario: View IMA log with search
    Given the user is viewing agent "a1b2c3d4" detail page
    When the user selects the "IMA Log" tab
    Then the IMA measurement list entries MUST be displayed
    And each entry MUST indicate policy match or mismatch
    When the user searches for "/usr/bin/bash"
    Then only IMA entries for that file path MUST be shown

  Scenario: View raw JSON data
    Given the user is viewing agent "a1b2c3d4" detail page
    When the user selects the "Raw Data" tab
    Then the full agent JSON record from the Verifier API MUST be displayed
    And a "Copy" button MUST allow copying the JSON to clipboard
```

### FR-021: PCR Values Monitoring

**Description:** The System MUST display current PCR bank values (SHA-256) for each agent with an expected vs. actual comparison. Each PCR MUST show its description, status (Match/Changed), and last changed date. The System MUST support PCR banks across SHA-1, SHA-256, SHA-384, and SHA-512.

**Trace:** Agent Detail - PCR Values View

```gherkin
Feature: PCR Values Monitoring

  Scenario: Display PCR comparison table
    Given agent "a1b2c3d4" has PCR values reported by the Verifier
    When the user views the PCR Values tab
    Then a table MUST display each PCR index, description, match status, and last changed date
    And PCR 10 (IMA measurements) MUST show "Match" with a recent timestamp
    And any PCR with a value differing from expected MUST show "Changed"
```

### FR-022: PCR Change Detection

**Description:** The System MUST monitor PCR values for each agent and detect changes relative to expected baseline values. When a PCR value changes, the System MUST highlight the changed PCR, display the last changed timestamp, and allow the administrator to acknowledge the change or initiate an investigation. The System MUST alert if the IMA policy needs updating for legitimate changes.

**Trace:** Agent Detail - PCR Values View

```gherkin
Feature: PCR Change Detection

  Scenario: Detect PCR drift from baseline
    Given agent "a1b2c3d4" has a policy-defined expected value for PCR 8
    And the agent's current PCR 8 value differs from the expected value
    When the user views the PCR Values tab for agent "a1b2c3d4"
    Then PCR 8 MUST display with a "Changed" indicator
    And the last changed date MUST be displayed
    And "Acknowledge" and "Investigate" action buttons MUST be available
```

### FR-023: Cross-Tab Navigation

**Description:** The System SHOULD interconnect agent detail tabs so that clicking a PCR value in the PCR Values tab navigates to the corresponding IMA entries in the IMA Log tab, and certificate warnings link directly to the Certificates tab for renewal.

**Trace:** Agent Detail - Tabs and Sub-Views (2/2)

```gherkin
Feature: Cross-Tab Navigation

  Scenario: Navigate from PCR to IMA entries
    Given the user is viewing the PCR Values tab for agent "a1b2c3d4"
    And PCR 10 is highlighted with a "Changed" status
    When the user clicks on the PCR 10 value
    Then the view MUST switch to the IMA Log tab
    And the IMA entries corresponding to PCR 10 measurements MUST be displayed

  Scenario: Navigate from cert warning to Certificates tab
    Given the user sees a certificate expiry warning on the overview
    When the user clicks the warning link
    Then the view MUST switch to the Certificates tab
    And the expiring certificate MUST be highlighted
```

### FR-024: Attestation Analytics Overview

**Description:** The System MUST provide an attestation analytics overview displaying: total successful attestations, total failed attestations, average latency, and success rate as summary KPIs; an hourly attestation volume bar chart; a failure reason breakdown (donut chart); a latency distribution histogram; and a top failing agents ranked list.

**Trace:** Attestation Analytics - Overview Dashboard

```gherkin
Feature: Attestation Analytics Overview

  Scenario: Display attestation summary KPIs
    Given there were 12,450 successful and 38 failed attestations in the last 24 hours
    When the user navigates to the Attestation Analytics view
    Then the summary MUST show "12,450" successful, "38" failed, and the computed average latency
    And the success rate MUST display as "99.7%"

  Scenario: Display top failing agents
    Given multiple agents have different failure counts
    When the user views the Attestation Analytics
    Then a "Top Failing Agents" list MUST rank agents by failure count descending
```

### FR-025: Failure Categorization

**Description:** The System MUST categorize attestation failures by type and severity: Quote Invalid (Critical), Policy Violation (Critical), Evidence Chain broken (Critical), Boot Violation (High), Timeout (Medium), PCR Mismatch (Medium), and Clock Skew (Low). Each failure type MUST include a description and common cause.

**Trace:** Attestation Analytics - Failure Analysis

```gherkin
Feature: Failure Categorization

  Scenario: Classify failure by type
    Given agent "agent-042" fails with a TPM quote signature mismatch
    When the failure is processed by the analytics engine
    Then the failure MUST be categorized as "Quote Invalid"
    And the severity MUST be set to "Critical"
    And the common cause MUST indicate "TPM hardware issue, key mismatch"
```

### FR-026: Automatic Failure Correlation

**Description:** The System MUST automatically correlate attestation failures across agents using four correlation dimensions: Temporal (failures within the same time window), Causal (same failure reason across agents), Topological (failures grouped by datacenter, subnet, or verifier), and Policy-linked (failures matching a recent policy update). Correlated failures MUST be grouped into a single incident.

**Trace:** Attestation Analytics - Automatic Correlation; Attestation Analytics - Actionable Insights

```gherkin
Feature: Automatic Failure Correlation

  Scenario: Correlate failures after policy update
    Given IMA policy "production-v2" was updated 3 minutes ago
    And 15 agents sharing policy "production-v2" report IMA policy violation within a 2-minute window
    When the System processes these failure events
    Then the System MUST group all 15 failures into a single correlated incident
    And the suggested root cause MUST reference the recent policy update
    And the incident view MUST provide a one-click rollback option

  Scenario: Distinguish targeted attack from mass failure
    Given a single agent fails with a unique IMA hash mismatch
    And no other agents share the same failure reason or timing
    When the System processes the failure
    Then the System MUST NOT correlate it with other incidents
    And the alert MUST indicate "warrants immediate investigation"
```

### FR-027: Incident Grouping with Suggested Root Cause

**Description:** The System MUST group correlated failures into a single incident. Each incident MUST include a suggested root cause, the triggering event (e.g., policy change), and a recommended action. The System MUST reduce N individual alerts to 1 correlated incident and automatically link to the triggering change.

**Trace:** Attestation Analytics - Actionable Insights

```gherkin
Feature: Incident Grouping

  Scenario: Group policy-related failures
    Given 15 agents fail with IMA policy violation after policy "production-v2" was updated
    When the correlation engine processes the failures
    Then a single incident MUST be created grouping all 15 failures
    And the incident MUST link to the policy change event
    And the recommended action MUST suggest reviewing the latest policy change
```

### FR-028: One-Click Policy Rollback from Incident

**Description:** The System SHOULD provide a one-click rollback option from the incident view that reverts a policy to its previous version when a policy update is identified as the root cause of correlated failures.

**Trace:** Attestation Analytics - Actionable Insights

```gherkin
Feature: Policy Rollback from Incident

  Scenario: Rollback policy from incident view
    Given an incident is open with suggested root cause "policy update to production-v2"
    And the user has the Admin role
    When the user clicks "Rollback Policy" on the incident
    Then the System SHOULD revert "production-v2" to its previous version
    And the rollback MUST be recorded in the audit log
```

### FR-029: Push Mode (v3 API) Attestation Analytics

**Description:** The System MUST provide analytics specific to push-mode attestation including: attestation submission rate per agent, nonce expiry tracking, challenge lifetime compliance, evidence evaluation duration, rate limit violations per IP/agent. Push-mode alerts MUST include: agent not submitting attestations, nonce expired before evidence, repeated evaluation failures, rate limit threshold reached, and session token expiry warnings.

**Trace:** Attestation Analytics - Push Mode

```gherkin
Feature: Push Mode Analytics

  Scenario: Track nonce expiry rate
    Given the deployment operates in push mode (v3 API)
    And 10% of nonces expired before evidence was submitted
    When the user views push mode analytics
    Then the nonce expiry rate MUST be displayed as 10%
    And a warning alert MUST be raised for high nonce expiry

  Scenario: Alert on silent agent
    Given agent "agent-100" has not submitted attestation evidence for 10 minutes
    And the expected submission interval is 2 minutes
    When the System evaluates push mode agent activity
    Then the System MUST raise an alert indicating "agent not submitting attestations"
```

### FR-030: Verification Pipeline Visualization

**Description:** The System MUST visualize the multi-stage verification pipeline showing each stage: Receive Quote + Logs, Validate TPM Quote, Check PCR Values, Verify IMA Log, and Verify Measured Boot. Each stage MUST show pass/fail status and timing. The visualization MUST indicate where in the pipeline a failure occurred.

**Trace:** Verification Pipeline - Flow Visualization

```gherkin
Feature: Verification Pipeline Visualization

  Scenario: Display pipeline with failure point
    Given agent "agent-042" failed at the "Verify IMA Log" stage
    When the user views the verification pipeline for the latest attestation
    Then stages "Receive", "Validate TPM Quote", and "Check PCR Values" MUST show pass indicators
    And stage "Verify IMA Log" MUST show a fail indicator
    And subsequent stages MUST be shown as not reached
```

### FR-031: Per-Stage Verification Metrics

**Description:** The System MUST track timing and success rates for each verification stage independently: Quote Validation (signature, nonce freshness), PCR Check (replay time, bank coverage), IMA Verification (entries processed, allowlist hit/miss ratio, excludelist matches, TOMTOU errors), and Measured Boot (event log entries, compliance rate, firmware update detections).

**Trace:** Verification Pipeline - Stage Metrics

```gherkin
Feature: Per-Stage Verification Metrics

  Scenario: Display IMA verification depth metrics
    Given the Verifier has processed attestations for agent "a1b2c3d4"
    When the user views verification stage metrics
    Then IMA stage MUST show entries processed count
    And IMA stage MUST show allowlist hit/miss ratio
    And IMA stage MUST show average processing time
```

### FR-032: IMA Quote Progress Tracking

**Description:** The System MUST track the gap between total IMA measurement entries and verified entries per agent. If the gap exceeds a configurable threshold (default: 1000 entries or 5 minutes of growth), the System MUST raise an alert indicating potential network issues, agent overload, or intentional evasion.

**Trace:** Verification Pipeline - Quote Progress Tracking

```gherkin
Feature: IMA Quote Progress Tracking

  Scenario: Alert on IMA verification lag
    Given agent "agent-042" has 50,000 total IMA entries
    And only 48,500 entries have been verified
    And the gap threshold is configured at 1,000 entries
    When the System evaluates IMA progress for "agent-042"
    Then the System MUST raise an alert for IMA verification lag
    And the alert MUST display the gap size (1,500 entries)
```

### FR-033: IMA Policy List View

**Description:** The System MUST display a list of all IMA runtime policies showing: Policy Name, number of assigned Agents, Entry count, Checksum, Last Updated date, and Edit/View actions. A search bar MUST allow filtering policies by name. New Policy and Import buttons MUST be available.

**Trace:** Policy Management - IMA Policies

```gherkin
Feature: IMA Policy List View

  Scenario: Display policy list
    Given the Verifier has 5 IMA policies configured
    When the user navigates to the Policy Management view
    Then all 5 policies MUST be listed with name, agent count, entry count, checksum, and last updated date
    And each policy MUST have "Edit" and "View" action links

  Scenario: Search policies by name
    Given policies named "production-v2", "staging-v3", "minimal" exist
    When the user types "prod" in the policy search bar
    Then only "production-v2" MUST be displayed
```

### FR-034: Policy CRUD with Editor

**Description:** The System MUST provide full CRUD operations for IMA runtime policies via the dashboard. The editor MUST support: file upload for allowlist, inline editing with syntax highlighting, hash algorithm selection (SHA-256, SHA-384), exclude list configuration, IMA signature key management, policy validation before save, and automatic checksum computation.

**Trace:** Policy Management - Policy Editor

```gherkin
Feature: Policy Editor

  Scenario: Create new IMA policy via upload
    Given the user has the Admin role
    When the user clicks "New Policy" and uploads an allowlist file
    Then the System MUST parse and validate the file
    And the System MUST compute and display the checksum
    And the user MUST be able to save the policy

  Scenario: Validate policy before save
    Given the user is editing a policy with an invalid hash format
    When the user clicks "Save"
    Then the System MUST display a validation error
    And the policy MUST NOT be saved until errors are corrected
```

### FR-035: Policy Versioning

**Description:** The System MUST maintain a version history for each policy with change diffs. The System MUST support rollback to any previous version. An audit trail MUST record who changed what and when. A side-by-side comparison view MUST be available.

**Trace:** Policy Management - Policy Editor

```gherkin
Feature: Policy Versioning

  Scenario: View policy change diff
    Given policy "production-v2" has been updated 3 times
    When the user views the change history
    Then the System MUST list all 3 versions with timestamps and authors
    When the user selects two versions for comparison
    Then a side-by-side diff MUST highlight added, removed, and modified entries

  Scenario: Rollback to previous version
    Given the user has the Admin role
    And policy "production-v2" is at version 3
    When the user selects "Rollback to version 2"
    Then the policy MUST revert to version 2 content
    And a new version 4 MUST be created reflecting the rollback
```

### FR-036: Measured Boot Policy Management

**Description:** The System MUST support managing measured boot (MB) policies: list all policies, create from reference boot log, edit rules and constraints, associate with agent groups, and validate against known event logs.

**Trace:** Policy Management - Policy Editor

```gherkin
Feature: Measured Boot Policy Management

  Scenario: Create MB policy from reference boot log
    Given the user has the Admin role
    When the user uploads a reference UEFI event log
    Then the System MUST generate a measured boot policy from the log
    And the user MUST be able to edit and save the generated policy
```

### FR-037: Policy Assignment Matrix

**Description:** The System MUST display a matrix view showing which agents are assigned to which policies. The System MUST support batch reassignment, orphan policy detection (policies not assigned to any agent), impact analysis before changes, and preview of policy effect on agents.

**Trace:** Policy Management - Policy Editor

```gherkin
Feature: Policy Assignment Matrix

  Scenario: Detect orphan policies
    Given policy "dev-old" is not assigned to any agent
    When the user views the policy assignment matrix
    Then "dev-old" MUST be flagged as an orphan policy

  Scenario: Batch reassign agents to new policy
    Given 15 agents are assigned to policy "staging-v2"
    When the admin selects all 15 agents and assigns them to "staging-v3"
    Then all 15 agents MUST be reassigned to "staging-v3"
    And the assignment change MUST be recorded in the audit log
```

### FR-038: Pre-Update Policy Impact Analysis

**Description:** The System MUST perform an impact analysis before applying policy changes. The analysis MUST categorize affected agents into three groups: Unaffected (no files match changes), Affected (have modified files), and Will Fail (removed hash currently in use). The System MUST display a recommendation and provide Submit for Approval, Staged Rollout, and Cancel actions.

**Trace:** Policy Management - Impact Analysis

```gherkin
Feature: Policy Impact Analysis

  Scenario: Analyze impact of IMA policy update
    Given IMA policy "production-v2" is assigned to 185 agents
    And the proposed update adds 45 hashes, removes 12 hashes, and modifies 8 hashes
    When the administrator requests impact analysis for the proposed change
    Then the System MUST display the number of unaffected agents
    And the System MUST display the number of affected agents
    And the System MUST display the number of agents that will fail
    And a recommendation MUST be provided based on the analysis
```

### FR-039: Two-Person Rule for Policy Changes

**Description:** The System MUST enforce a two-person approval workflow for policy changes. Admin A drafts the policy change, the System runs impact analysis automatically, and Admin B (a different user) reviews and approves. The System MUST support configurable quorum (e.g., 2-of-3, 3-of-5). The approver MUST NOT be the same user as the drafter. Approval requests MUST have a time-limited window (default: 24 hours) after which they expire.

**Trace:** Policy Management - Two-Person Rule

```gherkin
Feature: Two-Person Policy Approval

  Scenario: Policy change requires different approver
    Given Admin A drafts a change to IMA policy "production-v2"
    And the System runs impact analysis automatically
    When Admin A attempts to approve their own policy change
    Then the System MUST reject the approval
    And the System MUST display an error indicating the approver cannot be the drafter

  Scenario: Approval window expiry
    Given Admin A submitted a policy change for review
    And 24 hours have elapsed without approval
    When Admin B attempts to approve the change
    Then the System MUST reject the approval as expired
    And the policy change MUST revert to Draft state

  Scenario: Successful two-person approval
    Given Admin A drafts a policy change
    And Admin B reviews and approves the change within 24 hours
    When the approval is recorded
    Then the System MUST automatically push the policy to the Verifier
    And the audit log MUST record both the drafter and approver identities
```

### FR-040: Time-Limited Approval Window

**Description:** The System MUST enforce a configurable time-limited approval window for policy changes. The default window MUST be 24 hours. Policy changes not approved within the window MUST automatically expire and revert to Draft state.

**Trace:** Policy Management - Two-Person Rule

```gherkin
Feature: Approval Window Expiry

  Scenario: Policy approval expires after 24 hours
    Given Admin A submitted a policy change for review at 10:00
    And no approver acts within 24 hours
    When the 24-hour window elapses
    Then the policy change MUST automatically expire
    And the status MUST revert to "Draft"
    And an audit log entry MUST record the expiration
```

### FR-041: Change Management Integration

**Description:** The System MAY integrate with external change management systems. Integration options MUST include: optionally gating policy push on ServiceNow Change Request approval, auto-creating Jira tickets for policy review, linking CR numbers in the audit log, and supporting emergency bypass with break-glass audit.

**Trace:** Policy Management - Two-Person Rule

```gherkin
Feature: Change Management Integration

  Scenario: Gate policy push on ServiceNow CR
    Given ServiceNow integration is enabled
    And Admin A drafts a policy change
    When the change is submitted for approval
    Then a ServiceNow Change Request MUST be auto-created
    And the policy push MUST be blocked until the CR is approved in ServiceNow
```

### FR-042: Security Audit Event Log

**Description:** The System MUST maintain a searchable security audit event log. Each event MUST include severity level (CRITICAL, WARNING, INFO), timestamp, source component, action performed, and acting user identity. The log MUST be filterable by severity, category, and date range. An Export function MUST be available.

**Trace:** Security Audit - Event Log Dashboard

```gherkin
Feature: Security Audit Event Log

  Scenario: Filter audit log by severity
    Given the audit log contains events of various severities
    When the user selects severity filter "CRITICAL"
    Then only events with CRITICAL severity MUST be displayed
    And WARNING and INFO events MUST be hidden

  Scenario: Audit log captures attestation failure
    Given agent "a1b2c3d4" fails attestation with an IMA policy violation
    When the verifier reports the failure
    Then a CRITICAL audit event MUST be logged
    And the event MUST include the source (verifier ID), action (VERIFY_EVIDENCE), agent ID, and failure reason
```

### FR-043: Authorization Tracking

**Description:** The System MUST track all authorization decisions made by Keylime's authorization provider. Tracked actions MUST include: Agent Management (CREATE, READ, UPDATE, DELETE by admin mTLS), Attestation (SUBMIT, READ, LIST by agent PoP token), Policy Management (CREATE, READ, UPDATE, DELETE by admin mTLS), Sessions (CREATE, EXTEND by agent), Verification (VERIFY_IDENTITY, VERIFY_EVIDENCE by admin mTLS), and Registration (REGISTER, ACTIVATE, DELETE by agent/admin).

**Trace:** Security Audit - Authorization Tracking

```gherkin
Feature: Authorization Tracking

  Scenario: Track admin policy update
    Given admin "admin@example.com" updates IMA policy "production-v2"
    When the authorization event is recorded
    Then the audit log MUST contain action "UPDATE" in category "Policy Management"
    And the identity type MUST be "admin (mTLS)"
    And the actor MUST be "admin@example.com"
```

### FR-044: Identity Verification Event Monitoring

**Description:** The System MUST monitor agent identity verification events including: new agent registration (with identity type: ek_cert, iak_idevid, default), agent re-registration (regcount tracking), EK certificate validation results, and TPM key changes between registrations.

**Trace:** Security Audit - Identity Verification Events

```gherkin
Feature: Identity Verification Events

  Scenario: Track new agent registration
    Given a new agent registers with identity type "iak_idevid"
    When the registration event is processed
    Then the audit log MUST record action "REGISTER_AGENT"
    And the identity type MUST be "iak_idevid"

  Scenario: Alert on re-registration from different IP
    Given agent "agent-042" was originally registered from IP 10.0.1.5
    When agent "agent-042" re-registers from IP 10.0.2.10
    Then the System MUST raise a WARNING alert
    And the alert MUST indicate the IP change from 10.0.1.5 to 10.0.2.10
```

### FR-045: Anomaly Detection

**Description:** The System SHOULD detect anomalous authorization patterns including: unusual authorization sequences, failed authorization attempts, privilege escalation attempts, and off-hours administrative actions.

**Trace:** Security Audit - Authorization Tracking

```gherkin
Feature: Anomaly Detection

  Scenario: Detect off-hours admin action
    Given the organization's business hours are 08:00-18:00
    When admin "admin@example.com" deletes an agent at 03:00
    Then the System SHOULD flag the action as an off-hours anomaly
    And a WARNING alert SHOULD be raised for security review
```

### FR-046: Revocation Notification Channel Monitoring

**Description:** The System MUST monitor all revocation notification channels: Agent (REST) direct notification with delivery status and latency, ZeroMQ notification with connection state and queue depth, and Webhook notification with HTTP status and retry count.

**Trace:** Revocation - Notification Channels

```gherkin
Feature: Revocation Channel Monitoring

  Scenario: Monitor webhook delivery failure
    Given a revocation webhook is configured for "hooks.slack.com"
    And a revocation event triggers for agent "agent-042"
    When the webhook delivery fails with HTTP 503
    Then the System MUST display "failed (retry 1)" for the webhook channel
    And the System MUST retry delivery according to the configured retry policy

  Scenario: Monitor ZeroMQ connection state
    Given ZeroMQ is configured on port 8992
    When the ZeroMQ connection is active
    Then the integration status MUST show "connected" with current queue depth
```

### FR-047: Alert Lifecycle Workflow

**Description:** The System MUST implement an alert lifecycle: New → Acknowledged → Under Investigation → Resolved or Dismissed. Operators MUST be able to acknowledge alerts, assign them to team members, add investigation notes, reactivate agents after fix, update policies for false positives, and escalate to security teams.

**Trace:** Revocation - Alert Workflow

```gherkin
Feature: Alert Lifecycle Workflow

  Scenario: Acknowledge and investigate an alert
    Given a new critical alert exists for agent "agent-042"
    And the user has the Operator role
    When the operator clicks "Acknowledge" on the alert
    Then the alert state MUST change to "Acknowledged"
    When the operator assigns the alert and clicks "Investigate"
    Then the alert state MUST change to "Under Investigation"

  Scenario: Auto-resolve on re-attestation
    Given agent "agent-042" has an active alert for attestation failure
    When agent "agent-042" passes a subsequent attestation cycle
    Then the System SHOULD automatically resolve the alert
    And the resolution reason MUST indicate "auto-resolved on successful re-attestation"
```

### FR-048: Alert Auto-Escalation

**Description:** The System SHOULD automatically escalate alerts that remain unacknowledged after a configurable SLA timeout. Escalation MUST follow a defined escalation chain.

**Trace:** Revocation - Alert Workflow

```gherkin
Feature: Alert Auto-Escalation

  Scenario: Escalate unacknowledged critical alert
    Given a CRITICAL alert has been in "New" state for 30 minutes
    And the SLA timeout for critical alerts is configured at 15 minutes
    When the SLA timeout is exceeded
    Then the System SHOULD escalate the alert to the next level in the escalation chain
    And a notification SHOULD be sent to the escalation recipient
```

### FR-049: Alert Auto-Resolve

**Description:** The System SHOULD automatically resolve alerts when the underlying condition clears, specifically when an agent passes a subsequent attestation cycle. The System MUST implement notification deduplication and severity auto-adjustment.

**Trace:** Revocation - Alert Workflow

```gherkin
Feature: Alert Auto-Resolve

  Scenario: Auto-resolve on successful attestation
    Given agent "agent-067" has an active warning alert for boot violation
    When agent "agent-067" completes a successful attestation
    Then the System SHOULD change the alert state to "Resolved"
    And the resolution MUST be marked as "auto-resolved"
```

### FR-050: Unified Certificate View

**Description:** The System MUST provide a unified view of all certificate types in the Keylime ecosystem: EK Certificate (TPM vendor), AK Certificate (Keylime CA), mTLS Certificate (Agent↔Verifier), IAK Certificate (manufacturer), IDevID Certificate (manufacturer), and Server Certificates (Verifier/Registrar from Org CA).

**Trace:** Certificate Management - Overview

```gherkin
Feature: Unified Certificate View

  Scenario: Display all certificate types
    Given agents have EK, AK, and mTLS certificates
    And the Verifier and Registrar have server certificates
    When the user navigates to the Certificate Management view
    Then all certificate types MUST be listed in a unified view
    And each certificate MUST show its type, associated entity, and validity status
```

### FR-051: Certificate Expiry Dashboard

**Description:** The System MUST display a certificate expiry dashboard showing summary counts (Expired, Expiring within 30 days, Valid, Total) and a detailed list of certificates requiring attention. The System MUST display a 90-day certificate expiry timeline. Alert thresholds MUST be tiered: 90-day informational, 30-day action required, 7-day critical, 1-day emergency, and expired.

**Trace:** Certificate Management - Expiry Dashboard; Certificate Management - Operations

```gherkin
Feature: Certificate Expiry Dashboard

  Scenario: Display certificate expiry summary
    Given the fleet has 1 expired certificate, 5 certificates expiring within 30 days, and 244 valid certificates
    When the user navigates to the Certificate Expiry Dashboard
    Then the summary MUST show "Expired: 1", "Expiring <30d: 5", "Valid: 244", "Total: 250"

  Scenario: Certificate expiry alert at 7-day threshold
    Given agent "agent-015" has an mTLS certificate expiring in 7 days
    When the System evaluates certificate validity
    Then a critical alert MUST be raised for agent "agent-015"
    And the alert MUST indicate the certificate type and expiry date
```

### FR-052: Certificate Detail Inspection

**Description:** The System MUST provide a detailed certificate inspection view showing: Subject and Issuer DN, serial number, validity period (Not Before/After), public key algorithm and size, signature algorithm, Subject Alternative Names (SANs), Key Usage / Extended Key Usage, certificate chain visualization, and PEM/DER export options. For EK certificates, the System MUST verify the TPM vendor and validate the chain against known CAs.

**Trace:** Certificate Management - Operations

```gherkin
Feature: Certificate Detail Inspection

  Scenario: Inspect EK certificate
    Given agent "a1b2c3d4" has an EK certificate issued by a TPM vendor
    When the user views the certificate detail for the EK cert
    Then the Subject DN, Issuer DN, serial number, and validity period MUST be displayed
    And the certificate chain visualization MUST show the chain to the root CA
    And PEM export MUST be available

  Scenario: Validate EK certificate chain
    Given the TPM vendor CA certificates are pre-loaded
    When the System validates agent "a1b2c3d4"'s EK certificate
    Then the chain validation result MUST be displayed (valid/invalid)
```

### FR-053: Automated Certificate Renewal

**Description:** The System SHOULD support automated certificate renewal workflows for Keylime CA-issued certificates. Renewal MUST include: approval workflow, batch scheduling, pre-renewal validation, and rollback on failure.

**Trace:** Certificate Management - Operations

```gherkin
Feature: Automated Certificate Renewal

  Scenario: Auto-renew expiring mTLS certificate
    Given agent "agent-015" has an mTLS certificate expiring in 7 days
    And auto-renewal is enabled for Keylime CA certificates
    When the System initiates renewal
    Then the certificate SHOULD be renewed with approval workflow
    And the new certificate MUST be validated before activation

  Scenario: Rollback on renewal failure
    Given a certificate renewal fails validation
    When the System detects the failure
    Then the System SHOULD rollback to the previous certificate
    And an alert MUST be raised indicating renewal failure
```

### FR-054: Pull Mode Attestation Monitoring

**Description:** The System MUST provide monitoring specific to pull-mode (v2 API) attestation including: quote polling frequency compliance, agent response time distribution, state transition timeline, retry count and backoff status, V-key delivery tracking, and concurrent agent polling load. Alerts MUST include: agent unresponsive, quote interval drift, exponential backoff triggered, max retries exceeded, verifier overloaded, and agent stuck in RETRY state.

**Trace:** Attestation Modes - Pull Mode Monitoring

```gherkin
Feature: Pull Mode Monitoring

  Scenario: Alert on max retries exceeded
    Given agent "agent-042" has exceeded the configured max_retries of 5
    When the System evaluates pull mode agent health
    Then a WARNING alert MUST be raised indicating "max retries exceeded"

  Scenario: Detect verifier overload
    Given the verifier's pending verification queue is growing
    And the queue depth exceeds the configured threshold
    When the System monitors pull mode metrics
    Then a WARNING alert MUST indicate "verifier overloaded (queue growing)"
```

### FR-055: Push Mode Attestation Monitoring

**Description:** The System MUST provide monitoring specific to push-mode (v3 API) attestation including: agent submission frequency, nonce expiry rate, evidence evaluation time, session token lifecycle, challenge lifetime utilization, and capabilities negotiation outcomes. Alerts MUST include: agent silent, high nonce expiry rate, rate limit violations, session creation failures, verification timeout exceeded, and evidence chain broken.

**Trace:** Attestation Modes - Push Mode Monitoring

```gherkin
Feature: Push Mode Monitoring

  Scenario: Alert on rate limit violations
    Given agent "agent-100" exceeds the per-agent rate limit for attestation submissions
    When the rate limit violation is detected
    Then a WARNING alert MUST be raised
    And the alert MUST indicate the agent ID and the limit threshold

  Scenario: Track session token lifecycle
    Given push mode sessions are active
    When the user views push mode monitoring
    Then each session MUST show creation time, expiry time, and associated agent UUID
```

### FR-056: Mixed Mode Unified Views

**Description:** In deployments with both pull-mode and push-mode agents, the System MUST provide unified views that normalize metrics across modes. The System MUST also offer mode-specific drill-down views. The dashboard MUST adapt its displays based on the configured attestation mode.

**Trace:** Attestation Modes - Comparative View

```gherkin
Feature: Mixed Mode Unified Views

  Scenario: Unified fleet view with mixed modes
    Given the deployment has 200 agents in pull mode (v2 API) and 50 agents in push mode (v3 API)
    When the user views the Fleet Overview Dashboard
    Then all 250 agents MUST appear in the unified agent list
    And each agent MUST indicate its attestation mode
    And KPIs MUST aggregate across both modes

  Scenario: Mode-specific drill-down
    Given the dashboard detects agents operating in push mode
    When the user navigates to the Attestation Analytics view
    Then a mode toggle MUST allow switching between unified, pull-only, and push-only views
```

### FR-057: Backend Connectivity Status Dashboard

**Description:** The System MUST display a real-time connectivity status dashboard for all Keylime backend services. Monitored services MUST include: Core Services (Verifier, Registrar) with endpoint address, UP/DOWN/HIGH LOAD status, and uptime; Database Backends (Verifier DB, Registrar DB) with average query time and migration revision; Durable Attestation Backends (Rekor, Redis, RFC 3161 TSA) with latency and connection state; and Notification Channels (ZeroMQ, Webhook, Agent notifications) with delivery status. Each service MUST display a health indicator (green/yellow/red).

**Trace:** Integration Status - Backend Connectivity

```gherkin
Feature: Backend Connectivity Status

  Scenario: Display core service connectivity
    Given the Verifier is running at 10.0.0.1:8881 with 45 days uptime
    And a second Verifier at 10.0.0.3:8881 is under HIGH LOAD at 72%
    When the user navigates to the Integration Status view
    Then the Verifier (10.0.0.1:8881) MUST show status "UP" with uptime "45d"
    And the Verifier-02 (10.0.0.3:8881) MUST show status "HIGH LOAD" with a yellow indicator

  Scenario: Alert on backend service failure
    Given the RFC 3161 TSA at tsa.example.com is configured
    When the TSA connection times out
    Then the TSA entry MUST show status "TIMEOUT" with a red indicator
    And a WARNING alert MUST be raised indicating TSA unavailability
```

### FR-058: Durable Attestation Backend Monitoring

**Description:** The System MUST monitor all durable attestation backends: Rekor (transparency log) with upload latency and inclusion proofs, Redis (time-series data) with latency, memory usage, and key count, SQL DB (persistent storage) with query time and storage growth, File-based (local audit trail) with disk usage and write speed, and RFC 3161 TSA (timestamping authority) with response time and token validity. Alerts MUST fire on: upload failures, log unavailable, connection lost, high latency, storage full, slow queries, disk full, permission errors, TSA unreachable, and certificate expired.

**Trace:** Integration Status - Durable Attestation

```gherkin
Feature: Durable Attestation Backend Monitoring

  Scenario: Monitor Rekor transparency log health
    Given Rekor is configured at transparency-log.example.com
    When the System polls Rekor status
    Then the dashboard MUST display upload latency and inclusion proof success rate
    And an alert MUST fire if Rekor uploads fail for more than 5 consecutive attempts

  Scenario: Alert on Redis connection loss
    Given Redis is configured at redis.internal:6379
    When the Redis connection is lost
    Then the System MUST display "disconnected" for the Redis backend
    And a CRITICAL alert MUST be raised indicating "attestation data backend unavailable"
```

### FR-059: Compliance Framework Mapping Reports

**Description:** The System MUST map attestation capabilities to specific compliance framework controls. Supported frameworks MUST include: NIST SP 800-155 (BIOS integrity measurement), NIST SP 800-193 (platform firmware resilience), PCI DSS 4.0 (Req 11.5 file integrity monitoring, Req 10.2 audit trail), SOC 2 Type II (CC7.1 monitoring activities, CC6.1 logical access controls), FedRAMP (CA-7 continuous monitoring, SI-7 software integrity), and CIS Controls v8 (2.5 allowlisted software). Each mapping MUST identify the relevant dashboard evidence.

**Trace:** Compliance - Framework Mapping

```gherkin
Feature: Compliance Framework Mapping

  Scenario: View PCI DSS compliance mapping
    Given the System has IMA attestation data for the fleet
    When the user selects the PCI DSS 4.0 compliance report
    Then the report MUST map Req 11.5 to IMA attestation history per agent
    And the report MUST map Req 10.2 to the tamper-evident audit log export
    And each control MUST indicate its coverage status (Covered / Partial / Gap)

  Scenario: View FedRAMP compliance mapping
    Given the System has real-time attestation metrics
    When the user selects the FedRAMP compliance report
    Then control CA-7 MUST map to real-time attestation success rate
    And control SI-7 MUST map to IMA policy enforcement reports
```

### FR-060: One-Click Compliance Report Export

**Description:** The System MUST support one-click export of compliance reports in PDF and CSV formats. Each report MUST include: attestation coverage summary, policy compliance matrix, exception list with justifications, and time-bounded audit evidence packages. Reports MUST be filterable by compliance framework.

**Trace:** Compliance - Framework Mapping

```gherkin
Feature: One-Click Compliance Report Export

  Scenario: Export PDF compliance report
    Given the user is viewing the SOC 2 Type II compliance mapping
    And the user has the Operator or Admin role
    When the user clicks "Export PDF"
    Then a PDF report MUST be generated containing the compliance mapping, attestation coverage summary, and exception list
    And the PDF MUST include a timestamp and the generating user's identity

  Scenario: Export time-bounded audit evidence package
    Given the user selects date range "2026-01-01" to "2026-03-31"
    When the user exports the compliance evidence package
    Then the export MUST include all audit log entries within the date range
    And the export MUST include attestation pass/fail summaries per agent for the period
```

### FR-061: Tamper-Evident Hash-Chained Audit Logging

**Description:** The System MUST implement tamper-evident audit logging where each log entry includes a SHA-256 hash of the previous entry. The chain root MUST be anchored to an external RFC 3161 timestamp. Periodic chain checkpoints MUST be submitted to a Rekor transparency log. Tamper detection MUST run on startup and periodically. Each entry MUST include: timestamp (UTC, millisecond precision), actor (user identity from OIDC), action (CRUD + target), resource, source IP, user agent, result, and previous entry hash.

**Trace:** Compliance - Tamper-Evident Audit Logging

```gherkin
Feature: Tamper-Evident Audit Logging

  Scenario: Detect audit log tampering
    Given the audit log contains 1,000 hash-chained entries
    When an attacker modifies entry #500
    Then the hash chain verification MUST detect the tamper at entry #501
    And the System MUST raise a CRITICAL alert indicating audit log integrity violation

  Scenario: Audit log entry structure
    Given user "admin@example.com" updates IMA policy "production-v2"
    When the audit log entry is created
    Then the entry MUST include timestamp in UTC with millisecond precision
    And the entry MUST include actor "admin@example.com"
    And the entry MUST include action "UPDATE_RUNTIME_POLICY"
    And the entry MUST include the SHA-256 hash of the previous entry
```

### FR-062: Incident Response Ticketing Integration

**Description:** The System SHOULD integrate with enterprise incident response and ticketing systems: ServiceNow (incident + change request), Jira (issue auto-creation), PagerDuty (alert routing), and OpsGenie (on-call escalation). Integration MUST support bidirectional status sync. Change management (ITSM) SHOULD gate policy changes on an approved change request. Emergency bypass MUST include a break-glass audit trail. Automated remediation SHOULD support: network quarantine via NAC integration, agent isolation, re-provisioning workflow triggers, configurable playbooks per failure type, and manual approval gate for destructive actions.

**Trace:** Incident Response - Integration

```gherkin
Feature: Incident Response Ticketing Integration

  Scenario: Auto-create Jira issue on attestation failure
    Given the Jira integration is configured with project "SECOPS"
    And agent "agent-042" fails attestation with a CRITICAL severity
    When the incident response workflow triggers
    Then a Jira issue SHOULD be created in project "SECOPS"
    And the issue SHOULD include agent UUID, failure type, timestamp, and link to dashboard

  Scenario: Bidirectional status sync with ServiceNow
    Given a ServiceNow incident INC0012345 was created for agent "agent-042"
    When the ServiceNow incident is resolved externally
    Then the dashboard alert status SHOULD sync to "Resolved"
    And the resolution source MUST be recorded as "ServiceNow INC0012345"
```

### FR-063: SIEM Integration

**Description:** The System MUST integrate with Security Information and Event Management (SIEM) systems. Supported formats and protocols MUST include: Syslog (CEF/LEEF format), Splunk HEC (HTTP Event Collector), Elastic Common Schema (ECS), Prometheus metrics endpoint, and OpenTelemetry traces. SIEM integration MUST be available from Day 1 of deployment.

**Trace:** Incident Response - Integration

```gherkin
Feature: SIEM Integration

  Scenario: Export events via Syslog CEF format
    Given Syslog integration is configured with endpoint "siem.example.com:514"
    When an attestation failure event occurs for agent "agent-042"
    Then the System MUST emit a Syslog message in CEF format
    And the message MUST include severity, agent UUID, failure type, and timestamp

  Scenario: Expose Prometheus metrics endpoint
    Given the Prometheus integration is enabled
    When Prometheus scrapes the /metrics endpoint
    Then the endpoint MUST expose attestation success rate, alert counts, agent fleet size, and API response times
    And each metric MUST include appropriate labels (agent_id, severity, mode)
```

### FR-064: Verifier Cluster Performance Monitoring

**Description:** The System MUST monitor verifier cluster performance including: CPU utilization per worker process, memory usage (RSS, heap), open file descriptors, thread pool utilization, network connections (active/idle), attestations per second, queue depth (pending verifications), worker split (web vs. verification), `dedicated_web_workers` utilization, exponential backoff events, and rate limit rejections.

**Trace:** System Performance - Verifier Metrics

```gherkin
Feature: Verifier Cluster Performance Monitoring

  Scenario: Display verifier node resource metrics
    Given the deployment has two verifier nodes and one registrar
    When the user navigates to System Performance
    Then each verifier node MUST display CPU utilization, memory usage, and connection counts
    And the registrar MUST display its own resource metrics independently

  Scenario: Alert on verifier overload
    Given Verifier-02 CPU utilization exceeds 70%
    When the System evaluates verifier health
    Then Verifier-02 MUST display a yellow "HIGH LOAD" indicator
    And a WARNING alert MUST be raised indicating the affected node
```

### FR-065: Database Connection Pool Monitoring

**Description:** The System MUST monitor the Keylime database connection pool: active/idle connections, pool size and overflow count, connection wait time, pool exhaustion events, and `database_pool_sz_ovfl` settings. Query performance metrics MUST include: slow query detection (>100ms), query count by type (SELECT/UPDATE), table row counts, index hit ratio, and migration status (Alembic revision).

**Trace:** System Performance - Verifier Metrics

```gherkin
Feature: Database Connection Pool Monitoring

  Scenario: Display connection pool status
    Given the database connection pool has 10 active and 4 idle connections out of 14 total
    And overflow is at 0
    When the user views the Database Connection Pool panel
    Then the panel MUST display "Active: 10/14 | Idle: 4 | Overflow: 0"
    And average query time and max query time MUST be displayed

  Scenario: Alert on pool exhaustion
    Given all 14 connections are active and overflow reaches the configured maximum
    When a new connection request is queued
    Then the System MUST raise a CRITICAL alert indicating "database pool exhausted"
    And the connection wait time MUST be displayed
```

### FR-066: API Response Time Tracking

**Description:** The System MUST track API response times at p50, p95, and p99 percentiles for all Keylime API endpoints. Tracked endpoints MUST include at minimum: `GET /agents/`, `GET /agents/:id`, `POST /attestations`, `PATCH /attestations/:idx`, `GET /policies/ima`, and `POST /sessions`. Response times MUST be color-coded: green (<threshold), yellow (warning), red (critical).

**Trace:** System Performance - Verifier Metrics

```gherkin
Feature: API Response Time Tracking

  Scenario: Display API response time percentiles
    Given the System has collected response time data for GET /agents/
    When the user views the API Response Times panel
    Then p50, p95, and p99 response times MUST be displayed for GET /agents/
    And each percentile MUST be color-coded based on configured thresholds

  Scenario: Alert on degraded API response times
    Given the p99 response time for PATCH /attestations/:idx exceeds 1200ms
    And the critical threshold is configured at 1000ms
    When the System evaluates API performance
    Then the PATCH /attestations/:idx p99 value MUST display in red
    And a WARNING alert MUST be raised indicating API latency degradation
```

### FR-067: Live Configuration View with Drift Detection

**Description:** The System MUST display the current runtime configuration of the Verifier and Registrar, including key settings such as `quote_interval`, `retry_interval`, `max_retries`, `num_workers`, `database_pool_sz_ovfl`, `challenge_lifetime`, `mode`, and `enable_agent_mtls`. Each setting MUST indicate whether it is at its default value or modified. The System MUST alert on unplanned configuration changes that differ from the expected baseline.

**Trace:** System Performance - Configuration Monitoring

```gherkin
Feature: Configuration Drift Detection

  Scenario: Detect non-default configuration
    Given the verifier has "num_workers" set to 8 (default: 0/auto)
    And the verifier has "mode" set to "push" (default: "pull")
    When the user views the configuration monitoring panel
    Then "num_workers" MUST display with a "Modified" indicator
    And "mode" MUST display with a "Modified" indicator
    And settings at their default values MUST display with a "Default" indicator
```

### FR-068: Capacity Planning Projections

**Description:** The System SHOULD provide capacity planning projections based on current attestation rate and agent growth trends. Projections SHOULD indicate when the verifier will need horizontal scaling, when database storage will reach capacity, and when connection pool limits will be approached.

**Trace:** System Performance - Key Metrics

```gherkin
Feature: Capacity Planning Projections

  Scenario: Project verifier scaling needs
    Given the current attestation rate is 500 attestations/second
    And the agent fleet is growing at 50 agents/week
    When the user views the Capacity Planning panel
    Then the System SHOULD display a projected date for when the verifier will reach capacity
    And the projection SHOULD include current utilization trend and estimated headroom

  Scenario: Project database storage growth
    Given TimescaleDB is currently using 120GB of 500GB available storage
    And storage is growing at 2GB/day
    When the user views storage projections
    Then the System SHOULD project the date when storage will reach 80% capacity
    And a WARNING SHOULD be raised if the projected date is within 30 days
```

### FR-069: Agent State Machine Visualization

**Description:** The System MUST provide visual state machine diagrams for agent lifecycle states. Pull mode (v2 API) MUST display all 10 states: REGISTERED (0), START (1), SAVED (2), GET_QUOTE (3), RETRY (4), PROVIDE_V (5), FAILED (7), TERMINATED (8), INVALID_QUOTE (9), and TENANT_FAILED (10) with transition arrows. Push mode (v3 API) MUST display the 3-stage flow: capabilities submission, challenge/nonce response, and evidence evaluation. Failed states MUST be visually distinguished (e.g., red). The current state distribution across the fleet MUST be overlaid on the diagram.

**Trace:** Keylime - Agent State Machine; Attestation Modes - Push Mode

```gherkin
Feature: Agent State Machine Visualization

  Scenario: Display pull mode state distribution
    Given 247 agents are in GET_QUOTE state, 3 in FAILED, and 1 in RETRY
    When the user views the Agent State Machine visualization for pull mode
    Then all 10 states MUST be displayed as a directed graph with transition arrows
    And GET_QUOTE MUST show count "247"
    And FAILED MUST show count "3" with a red visual indicator
    And RETRY MUST show count "1"

  Scenario: Display push mode attestation flow
    Given the deployment includes push mode (v3 API) agents
    When the user views the push mode state visualization
    Then the 3-stage flow MUST be displayed: capabilities → challenge → evidence
    And each stage MUST show the current count of agents at that stage
```

### FR-070: API Version Distribution Visualization

**Description:** The System MUST display the distribution of Keylime API versions in use across the agent fleet. The visualization MUST show agent counts per API version (e.g., v2.0, v2.1, …, v2.5, v3.0) and the server's supported API versions. This enables operators to track fleet API migration progress.

**Trace:** Integration Status - Backend Connectivity

```gherkin
Feature: API Version Distribution Visualization

  Scenario: Display agent API version distribution
    Given the fleet has agents using API versions v2.0 through v3.0
    When the user views the API Version Distribution panel
    Then a bar chart MUST display agent counts per API version
    And the server's supported API versions MUST be indicated

  Scenario: Identify outdated agents
    Given the server supports v2.5 and v3.0
    And 15 agents are still using v2.0
    When the user views the API Version Distribution
    Then agents on v2.0 MUST be highlighted as running an older API version
    And the System SHOULD display a recommendation to upgrade
```

---

## 4. Non-Functional Requirements Detail

### NFR-003: Non-Invasive Architecture

**Description:** The System MUST operate as a standalone component that consumes Keylime's public REST APIs. The System MUST NOT require modifications to Keylime Verifier, Registrar, or Agent components. The System MUST NOT access Keylime databases directly.

**Trace:** Technical Architecture - System Design

### NFR-006: Event-Driven Ingestion

**Description:** The System MUST use event-driven ingestion as the primary data path. The System MUST consume ZeroMQ revocation events from the Verifier. The System SHOULD propose upstream state-change webhook/AMQP emit. A message broker (RabbitMQ/Kafka) SHOULD decouple load. The System MUST use event sequence numbers to detect gaps. A periodic reconciliation sweep MUST run every 5 minutes.

**Trace:** Technical Architecture - Event-Driven Ingestion; Scalability - Ingestion Model Comparison

### NFR-007: Polling Fallback with Backpressure

**Description:** For environments without event support, the System MUST implement adaptive polling with backpressure: list poll at 30s intervals using ETag/If-Modified-Since, detail polling on-demand only (user clicks), double the interval if p95 latency exceeds 500ms, and pause detail polling if p95 latency exceeds 2s. The System MUST alert the operator when degraded to polling mode.

**Trace:** Scalability - Ingestion Model Comparison; Technical Architecture - Event-Driven Ingestion

### NFR-012: Air-Gapped Deployment

**Description:** The System MUST be fully self-contained with no runtime internet access required. All frontend assets MUST be bundled (no CDN). The Rust backend MUST compile to a single binary with no runtime dependencies. Fonts and icons MUST be embedded. Pre-built container images MUST include all layers. Offline EK certificate validation MUST be supported via pre-loaded TPM vendor CA certificates. Update packages MUST be signed (GPG) with integrity verification and SBOM included.

**Trace:** Deployment - Offline & Air-Gapped

### NFR-014: Accessibility

**Description:** The dashboard UI MUST conform to WCAG 2.1 Level AA: keyboard navigation, screen reader support, color-contrast compliance, and ARIA labels on all interactive elements. This is REQUIRED for government procurement.

**Trace:** Deployment - Offline & Air-Gapped

### NFR-016: Graceful Degradation

**Description:** The System MUST degrade gracefully when backend components are unavailable: if TimescaleDB is down, show live data only with no history; if Redis is down, make direct API calls with no cache; if apalis workers are down, support manual refresh only; if the Keylime API is down, show cached state with an alert.

**Trace:** High Availability - Architecture

---

## 5. Security Requirements Detail

### SR-003: Three-Tier RBAC

**Description:** The System MUST enforce a three-tier RBAC model at the dashboard backend. Since Keylime's built-in authorization has no read-only admin role, the dashboard MUST proxy and restrict operations based on the user's dashboard role. The permissions matrix:

| Operation | Viewer | Operator | Admin |
|-----------|--------|----------|-------|
| View agent fleet, attestation analytics, policies, audit log | Yes | Yes | Yes |
| Export reports (CSV/PDF) | No | Yes | Yes |
| Acknowledge/manage alerts | No | Yes | Yes |
| Reactivate/stop agents | No | Yes | Yes |
| Create/edit/delete policies | No | No | Yes |
| Delete agents from verifier | No | No | Yes |
| Manage dashboard users/roles | No | No | Yes |
| Configure alert thresholds | No | No | Yes |
| View/export TPM key material | No | No | Yes |

All write operations to Keylime MUST be blocked at the dashboard proxy layer for non-Admin roles.

**Trace:** Dashboard RBAC - Role Definitions

### SR-005: mTLS Private Key Protection

**Description:** The mTLS private key grants full Keylime admin access. It MUST NEVER be stored on disk in cleartext. Supported storage backends: PKCS#11 HSM, HashiCorp Vault Transit, Kubernetes CSI Secret Store, or encrypted file with passphrase (development only). Rust's `rustls` MUST use a custom `SigningKey` trait for HSM/PKCS#11 offload.

**Trace:** Secret Management - Credential Lifecycle

### SR-013: Data Minimization

**Description:** The System MUST NEVER cache or store raw TPM quotes, IMA measurement logs, or raw boot event logs. These MUST be passed through from the Keylime API to the UI on demand and discarded. Only attestation results (pass/fail + timestamp) MUST be retained for historical analysis. PoP token hashes MUST NOT be displayed or cached; only session metadata (creation time, expiry, agent UUID) MAY be shown.

**Trace:** Threat Model - Data Classification; Attestation Modes - Comparative View

### SR-020: Data Classification

**Description:** The System MUST enforce the following data classification and protection requirements:

| Data Type | Classification | Storage Policy | Protection |
|-----------|---------------|----------------|------------|
| mTLS private key | SECRET | Never on disk | HSM or Vault only |
| EK/AK public keys | CONFIDENTIAL | Cache only (TTL) | Encrypt at rest |
| TPM quotes | CONFIDENTIAL | Do not cache | Pass-through only |
| IMA policy content | CONFIDENTIAL | Cache with TTL | Encrypt at rest |
| Agent IPs/UUIDs | INTERNAL | TimeSeries DB | Encrypt at rest |
| Attestation results | INTERNAL | TimeSeries DB | Encrypt at rest |
| Dashboard audit log | CONFIDENTIAL | Append-only store | Hash-chained, signed |
| Dashboard credentials | SECRET | Never persisted | OIDC session tokens |

**Trace:** Threat Model - Data Classification

### SR-025: TPM Identity Change Detection

**Description:** The System MUST alert on TPM identity changes during agent re-registration. CRITICAL alerts MUST fire when: TPM key changes on re-registration, or EK certificate mismatches on re-registration. WARNING alerts MUST fire when: regcount exceeds 3, or re-registration occurs from a different IP. TPM identity keys SHOULD be immutable; any change indicates potential compromise.

**Trace:** Security Audit - Identity Verification Events

```gherkin
Feature: TPM Identity Change Detection

  Scenario: Detect TPM key change on re-registration
    Given agent "agent-042" is registered with EK certificate "cert-A"
    When agent "agent-042" re-registers with a different EK certificate "cert-B"
    Then the System MUST raise a CRITICAL alert
    And the alert message MUST indicate "EK cert mismatch on re-registration"
    And the audit log MUST record both the previous and new certificate identifiers

  Scenario: Warn on high re-registration count
    Given agent "agent-042" has a registration count of 4
    When the System evaluates identity events
    Then the System MUST raise a WARNING alert indicating "high regcount (>3)"
```

---

## 6. Implementation Phasing

As defined in the source presentation, the implementation follows three phases:

**Phase 1 — Secure Foundation:** Agent fleet list, agent state monitoring, OIDC/SAML authentication, three-tier RBAC, tamper-evident audit log, mTLS to Keylime APIs. *Exit criteria: all API access authenticated, RBAC enforced on every endpoint, audit log captures all actions, mTLS key never on disk in cleartext, threat model reviewed, penetration test passed.*

**Phase 2 — Operations:** Attestation analytics, policy management UI, certificate monitoring, alert notifications, push mode (v3 API) support, SIEM integration, WebSocket updates, SSRF-validated webhooks, Prometheus metrics endpoint.

**Phase 3 — Enterprise Scale:** Multi-tenancy, compliance reports (NIST, PCI DSS, SOC 2, FedRAMP), incident response integration (ServiceNow, Jira, PagerDuty), HA deployment, air-gapped packaging, multi-cluster support, WCAG 2.1 AA accessibility.

**Trace:** Implementation Roadmap
