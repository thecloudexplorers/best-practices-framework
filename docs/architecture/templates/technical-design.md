# Technical Design Document Template

> Document structure and reading guide:
>
> - Chapters 1–3: Business Context & Requirements
> - Chapters 4–6: Architecture & Security
> - Chapters 7–10: Delivery & Operations
>
> For every chapter and sub-chapter starting from chapter 4, apply an **“If Applicable”** policy. If
> a chapter or sub-chapter is not applicable, list it in **Appendix 1: Not Applicable List**. After
> listing it, the chapter or sub-chapter may be removed to improve readability.
>
> For all chapters starting from chapter 4,**define or reference the approach**. To maintain a
> **single source of truth**, this template encourages referencing existing documents or processes.
>
> Examples:
>
> - **Architecture principles** – Refer to the organization’s architecture policy and reference
> architecture.
> - **Test strategy** – Refer to the organization’s test policy.
> - **Security & Compliance** – Refer to the organization’s security policy.
>
> **NOTE:** It is the author’s responsibility to ensure that all references are up-to-date **and**
> accessible to this document’s audience.

---

## 1. Executive Summary

> Describe the purpose, scope, and expected outcomes in business terms. State value, costs/benefits,
> key risks, and the decisions requested (go/no-go,budget, timeline). Keep this to a maximum of 1
> page.
>

### 3.1 Organizational Goals

> Define 3 to 5 **measurable** goals (OKR/KPI) with a baseline, target,
> deadline, and measurement method. Link each goal to a business value and an
> owner.

## 2. Stakeholders, Scope, and Assumptions

> Record who is involved and what they care about. Define in-scope / out-of-scope, dependencies, and
> explicit assumptions & constraints (including how/when you will validate them). Add a short RACI
> or owner per topic.

### 2.1 Stakeholders

> Identify who decides, who is accountable for data and risk, who runs the thing, and who guards
> architecture standards. For each stakeholder, record:
>
> - Role & Name/Org (who)
> - Accountability (what they own; use R/A/C/I if helpful)
> - Key Decisions (what they must sign off)
> - Inputs Needed (what you need from them to proceed)
> - Stage-Gate Sign-offs (e.g., HLD approval, Security Design Review, Go-Live)
>
> Keep it short. If someone can block the project (CISO, DPO, Architecture Board), they must be here.
> Tip: DPO duties come from GDPR Article 39; the CISO typically ensures BIO alignment and signs risk
> acceptance.
>
> | Role                   | Name / Organisation         | Accountability (R/A/C/I)                | Key Decisions                                             | Inputs Needed                                | Stage-Gate Sign-offs                                                       |
> | ---------------------- | --------------------------- | --------------------------------------- | --------------------------------------------------------- | -------------------------------------------- | -------------------------------------------------------------------------- |
> | **Business Owner**     | {Jane Doe}                  | **A** for business outcomes & budget    | Scope, funding, service KPIs                              | Business case, target KPIs, budget caps      | HLD approval, Go-Live                                                      |
> | **Data Owner**         | {Dept Head Social Services} | **A** for lawful processing & retention | Data categories, retention, sharing                       | Data classification, DPIA inputs             | HLD data section, DPIA sign-off                                            |
> | **CISO**               | {CISO Name}                 | **A** for information risk (BIO)        | Accept residual risks; crypto & network posture           | Threat model, controls mapping to BIO        | Security Design Review, Go-Live risk acceptance. ([Digital Government][1]) |

[1]: https://www.nldigitalgovernment.nl/overview/information-security__trashed/government-information-security-baseline/?utm_source=chatgpt.com "Government information security baseline"

### 2.2 Scope

> Describe what is in and out by domain, processes, systems, data, and interfaces. Be explicit to
> prevent scope creep.
>
> Include five subsections:
>
> - Domains (e.g., citizen case handling, permits)
> - Business Processes (e.g., "Register request", "Assess", "Decide", "Notify")
> - Systems (new and existing; Azure and non-Azure)
> - Data & Integrations (PII categories, Digikoppeling/eHerkenning/DigiD, BRP, etc.)
> - Environments (Dev/Test/Acc/Prod), Regions, and Tenancy (Landing Zone/subscriptions)
>
> Scope statement (one paragraph):
> "This project delivers an Azure-hosted digital case-management workload within the organization's
> Landing Zone, exposing REST APIs to internal systems and consuming BRP via Digikoppeling.
> Production runs in West Europe with North Europe as Disaster Recovery."
>
> *Example:*
>
> | Category                 | In Scope                                                                                                                   | Out of Scope                                |
> | ------------------------ | -------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
> | **Domains**              | Social-services case intake & tracking                                                                                     | Financial aid disbursement back-office      |
> | **Processes**            | Intake, verification, decision, notification                                                                               | Appeals & legal objection process           |
> | **Systems (Azure)**      | App Service (API/UI), Azure SQL, Key Vault, Application Gateway (WAF), API Management, Log Analytics, Monitor, Service Bus | AKS (no containers in v1)                   |
> | **Systems (External)**   | BRP lookup via Digikoppeling; municipal SSO via Azure AD / DigiD broker                                                    | Legacy document archive (read-only via URL) |
> | **Data**                 | PII: name, address, BSN (minimized), case metadata                                                                         | Scanned documents OCR (future phase)        |
> | **Interfaces**           | REST `/api/v1/cases` (internal), SOAP (BRP via Digikoppeling)                                                              | Batch exports to data warehouse             |
> | **Environments/Regions** | Dev/Test/Acc/Prod in West Europe; DR in North Europe                                                                       | On-prem DR                                  |
> | **Tenancy**              | Organizations tenant; production subscription in Landing Zone                                                               | Partner tenant hosting                      |

### 2.3 Assumptions

> Write testable assumptions that, if wrong, change cost, time, or design. Each assumption needs:
>
> - Statement (clear + testable)
> - Category (Cloud choice, Budget, Timeline, Vendor, Policy/Standard)
> - Evidence / Source (who said it / doc link)
> - Owner (who will validate) & Due Date
> - Risk if false (impact)
> - Mitigation / Plan B (what you’ll do)
> - Status (Unverified / Verified / Falsified → becomes Risk/Issue)
> - Align cloud/architecture assumptions with CAF and WAF so reviewers know you’re using Microsoft’s baseline guidance.
>
> *Example:*
>
> | ID   | Assumption (testable)                                                                                            | Category        | Evidence / Source                                 | Owner            | Validate by | Risk if false                            | Mitigation / Plan B                                                    | Status                                     |
> | ---- | ---------------------------------------------------------------------------------------------------------------- | --------------- | ------------------------------------------------- | ---------------- | ----------- | ---------------------------------------- | ---------------------------------------------------------------------- | ------------------------------------------ |
> | A-01 | **Azure Landing Zone (policy, RBAC, networking) is available by project start** and meets baseline CAF guidance. | Cloud choice    | EA says “ready” in kickoff; CAF landing zone docs | Platform Owner   | 2025-10-15  | Delay 4–6 weeks; rework network & policy | Stand up minimal landing zone via Bicep in proj. sub; time-box 2 weeks | Unverified. ([Microsoft Learn][1])         |
> | A-02 | **Digikoppeling connectivity to BRP** is available with valid PKIoverheid certs and mTLS.                        | Vendor/Standard | Integration team plan; Forum Standaardisatie      | Integration Lead | 2025-10-22  | No citizen lookup; blocked UAT           | Mock BRP; switch to delayed verification workflow                      | Unverified. ([forumstandaardisatie.nl][2]) |
> | A-03 | **DPO provides approved data classification & DPIA outcome** before dev complete.                                | Policy/GDPR     | DPO email; GDPR Art. 39 duties                    | DPO              | 2025-11-01  | Rework data model; go-live risk          | Use anonymised test data; freeze PII features                          | Unverified. ([GDPR][3])                    |
Run one full DR + one table-top; defer RTO to 8h if needed             | Unverified                                 |

[1]: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/overview?utm_source=chatgpt.com "Microsoft Cloud Adoption Framework for Azure"
[2]: https://www.forumstandaardisatie.nl/open-standaarden/digikoppeling?utm_source=chatgpt.com "Digikoppeling"
[3]: https://gdpr-info.eu/art-39-gdpr/?utm_source=chatgpt.com "Art. 39 GDPR – Tasks of the data protection officer"

## 3. Requirements

### 3.2 Functional Requirements (high level)

> Describe requirements as **verifiable capabilities** or **user stories**.
> For each requirement provide:
>
> - ID
> - Description
> - Priority (MoSCoW)
> - Acceptance criteria
>
> *Example:*
>
> | ID     | Requirement                                      | Priority          | Acceptance Criteria                                                                                                                                                 |
> | ------ | ------------------------------------------------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
> | FR-001 | Citizens can submit and track requests online    | Should         | Tracking ID returned within 5s; confirmation email delivered within 2m; request appears in staff queue within 60s; audit log entry (user, timestamp, IP) recorded. |
> | FR-002 | Staff workflow with SLA timers                   | Must  | SLA timer starts on case creation; visual countdown shown; warning at 80% of SLA and breach alert at 100%; SLA compliance report per team generated daily.         |

### 3.3 Non-Functional Requirements (high level)

> Specify **concrete, measurable** thresholds for availability, performance (P95), scalability,
> security, privacy, accessibility, and compliance. Add the verification method (measurement/test)
> and minimum standards (e.g., TLS version).
>
> *Example:*
>
> | ID      | Requirement            | Target         | Verification                                                                                                                                                 |
> | ------- | ---------------------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
> | NFR-001 | Availability           | ≥ 99.9%        | Synthetic uptime monitoring; monthly availability report shows ≥99.9%; quarterly failover drill evidence and post-mortem; Service Level Objectives tracked. |
> | NFR-002 | Performance (P95 API)  | ≤ 500 ms       | Load test (JMeter/Gatling) at ≥3× expected peak for 30 min with P95 ≤500 ms and error rate <1%; APM dashboard screenshots attached to test report.          |
> | NFR-003 | Scalability            | +3× peak load  | Stress test to 3× baseline; autoscale triggers within 5 min; queue backlog below defined threshold; no sustained CPU >80% for >15 min during test.          |
> | NFR-004 | Security & Privacy     | GDPR/BIO-compliant | SAST/DAST clean (no High/Critical); penetration test with 0 High findings before go-live; DPIA approved; TLS 1.2+ enforced; TDE/SSE enabled; access reviews passed. |

### 3.4 Constraints

> Record hard/standing constraints (cloud choice, standards, licenses) and assumptions with
> validation point and fallback. Link each item to a risk/mitigation.
>
> *Example:*
>
> | ID      | Constraint      | Notes                 | Risk/Mitigation                                                                 |
> | ------- | --------------------------- | --------------------- | -------------------------------------------------------------------------------- |
> | CST-001 | Cloud: Azure only           | Organization standard | Use containerization to reduce lock-in and preserve portability where feasible.  |
> | ASM-001 | Existing IdP: Entra ID      | MFA required          | No backup IdP defined in current cloud strategy → define failover auth plan.    |

## 4. Architecture

> **Executive View (why):** Target architecture with clear benefits (reliability, security, cost
> control, performance), based on **Azure Landing Zones** and **Well-Architected** pillars.

### 4.1 Context & Logical Architecture

> *Diagram placeholder:* Context & logical components (users, channels, web/API/processing, data,
> integrations, trust boundaries).

### 4.2 Component Catalog (overview)

> - **Frontends:** Portal (Static Web Apps), Staff UI (App Service).
> - **APIs & Processing:** App Service/AKS, Functions, Service Bus.
> - **Data:** Azure SQL/Cosmos DB, Storage (Blob/Files).
> - **Security:** Entra ID, Key Vault, Private Endpoints, WAF.
> - **Platform:** Landing zones (platform vs. application subscriptions), hub-spoke networking,
> policy & RBAC.
>
> **Implementation View (how):**\
> **Subscriptions & resource organization:** Mgmt Groups → Subscriptions (Platform vs Application
> LZs) → Resource Groups per workload/lifecycle.\
> **Network:** Hub-spoke VNets, subnets (web/app/data), UDRs; ingress via App Gateway (WAF), egress
> via Azure Firewall; Private DNS; Private Endpoints forPaaS.\
> **Compute:** App Service (Linux) with autoscale; AKS where orchestration is required; Functions
> for async.\
> **Data:** Azure SQL (TDE, Entra ID based auth), Cosmos DB (partition strategy), Storage (lifecycle
> policies).

## 5. Security & Compliance

> **Executive View (why):** Risk reduction and compliance: **BIO** (access control, logging,
> continuity, cryptography), **GDPR** (data minimization, DPIA/DSR), **Forum Standaardisatie**
> (Digikoppeling, DigiD/eHerkenning).

### 5.1 Identity & Access

> Describe authN/authZ flows (Entra ID, DigiD/eHerkenning where applicable), and address:
>
> - Roles and/or RBAC
> - Privileged Identity Management (PIM)
> - Just-in-Time (JIT)
> - Just Enough Administration (JEA)
> - Managed Identities
> - Secret management (Key Vault usage)

### 5.2 Data Protection

> Specify encryption in transit/at rest, key management (MMK/CMK, rotation), data masking in
> non-prod, logging/audit for sensitive actions, and PII limitations (data minimization).

### 5.3 Network Security

> Define segmentation (NSG/UDR), exposure points (WAF, Front Door, APIM), DDoS protection, egress
> controls, Private Link, and IP allowlists. WAF, NSGs, DDoS Protection, Private Link/Endpoints;
> restrict admin paths (VPN/ER, IP allowlisting).

### 5.4 Compliance Mapping (example)

| Requirement                 | Control (summary)                                  |
| --------------------------- | -------------------------------------------------- |
| BIO – Access Control        | RBAC least-privilege, MFA, PIM                     |
| BIO – Logging & Monitoring  | Central logs, immutability/retention, audit trails |
| GDPR – DSR/Right to Erasure | Automated delete/export, administrative workflow   |
| Forum Standaardisatie       | Digikoppeling for G2G, DigiD/eHerkenning for login |

> **Implementation View (how):** Concrete policies (Azure Policy), minimum TLS, cipher suites, WAF
> rules, log retention (e.g., 180 days), Defender for Cloud plans, audit configuration.

## 6. Data & Integration

> **Executive View (why):** Describe data domains, privacy impact, and critical integrations.

### 6.1 Data Models & Classification

> Provide a high-level ERD/JSON model. Label each entity with classification, source/target systems,
> quality requirements, and metadata/lineage.

### 6.2 Retention & Privacy

> Define retention periods, legal basis, archiving, DPIA reference, DSR process (access/export/
> delete), and audit trail (who did what, when).

### 6.3 Interfaces & Contracts

> Specify API contracts (OpenAPI), versions & backward compatibility, SLA/rate limiting/throttling,
> retry/backoff & idempotency, error codes, security (mTLS/OAuth/keys in Key Vault), and
> Digikoppeling where mandated. Add test/acceptance endpoints and smoke tests.

## 7. Delivery & Operations

> **Executive View (why):** Explain release cadence, operational KPIs, and cost control.

### 7.1 DevOps & CI/CD

> Define branching strategy, PR checks, pipeline definitions (build→test→security→deploy), quality
> gates (SAST/SCA/container scan), artifact storage, promotion flow, and manual approvals for
> production.

### 7.2 Infrastructure as Code & Environments

> Document IaC structure (folders, modules), variables/secrets (Key Vault/variables), drift
> detection, policy as code, tagging/naming conventions, and environment parity
> (Dev/Test/Pre-Prod/Prod).

### 7.3 Observability

> Describe monitoring (App Insights, Log Analytics), dashboards (KPIs, SLAs), alerting (thresholds,
> escalation), tracing (distributed), and log retention/archiving (security, compliance).
> **Implementation View (how):** Pipeline YAML, migrations (EF/DACPAC), slot-swap/blue-green/canary;
> alert rules (thresholds), runbooks (incident,cert rotation, key expiry).

## 8. Availability, DR & Backup

> Define RTO/RPO and describe HA design (AZ redundancy, min. 2 instances), DR topology
> (active-passive/active-active), failover/fallback process, exercise calendar, and backup strategy
> (scope, retention, encryption and restore procedures). Assign ownership.
>
> *Example:*
>
> - **HA:** ≥2 instances for critical services; AZ redundancy.
> - **DR:** Secondary region (active-passive), SQL geo-replication; **RTO/RPO** targets; failover
> playbooks and exercises.
> - **Backups:** Automated DB backups, Recovery Services Vault; periodic restore tests.

## 9. Test Strategy

> Define the test pyramid and coverage for FR/NFR. Describe test data policy (anonymized/synthetic),
> environments, tools, performance/soak/chaos tests, security tests (SAST/DAST/pen test), and exit
> criteria (release gate). Capture reporting & traceability (requirement → test).
>
> - **Unit/Contract/Integration/E2E**
> - **Performance/Soak/Chaos**
> - **Security:** Threat modeling, pen-test cadence, dependency & container scans.

## 10. Risks & Decisions

> Maintain and reference a risk register (impact, likelihood, mitigation, owner, target date) and
> open issues/dependencies. Record decisions in ADRs and reference them here. Describe change
> control (who/how approves).

## 11. Appendices

> **Appendix 1: Not Applicable List:** Chapters/sub-chapters that are not applicable.
> **Appendix 2: Glossary & Acronyms:** Define domain terms and abbreviations.
> **Appendix 3: References (authoritative):** Links to standards, policies, and best practices.
> **Appendix 4: Document Management:** Versioning, approval processes, and archiving.
