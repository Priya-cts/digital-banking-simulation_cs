# C4 – Level 1 System Context
## Indian Net Bank (iNB) Online Banking Application

This document defines the **C4 Model – Level 1 (System Context)** for the **Indian Net Bank (iNB) Online Banking Application**. It provides a high‑level view of the system of interest, its users, and the external systems it interacts with. This context is derived from the approved functional and non‑functional use‑cases and the clarified requirements.

---

## 1. System of Interest

**System Name:** Indian Net Bank (iNB) – Online Banking Application.

The iNB system enables digital banking services for customers and operational users. It supports customer onboarding, secure authentication with multi‑factor authentication, account management, statements and reporting, cheque processing, bill payments, money transfers, and compliance‑driven audit and monitoring capabilities.

---

## 2. Actors (People)

- **Customer (End User):** Uses online banking services such as registration, login, balance inquiry, bill payments, transfers, and cheque tracking.
- **Bank Staff / Admin:** Approves registrations, manages cheque processing, configures business rules, and performs operational tasks.
- **Auditor:** Reviews reconciliation reports, audit trails, and compliance evidence.
- **IT Operations / Support:** Monitors system health, performance, availability, backups, and disaster recovery.

---

## 3. External Systems and Dependencies

- **Identity Provider (IdP):** Bank’s existing Identity Provider (SAML‑based) for authentication, authorization, and session policies.
- **SMS Gateway:** Delivers One‑Time Passwords for MFA and SMS notifications such as cheque status updates.
- **Email Service:** Sends customer notifications including overdraft alerts, confirmations, and notices.
- **Payment Gateway – PayPal:** Processes electronic payments as the preferred payment gateway.
- **NEFT / RTGS Network:** Enables inter‑bank money transfers.
- **Bank Biller APIs:** Provides biller integration for bill presentment and payments.
- **Splunk:** Centralized logging and security information event management (SIEM).
- **AppDynamics:** Application performance monitoring, metrics, and tracing.

---

## 4. High‑Level Interactions

- Customers interact with the iNB system through web interfaces to perform banking operations.
- Bank staff access administrative functions for approvals, configuration, and reporting.
- The iNB system authenticates users via the Identity Provider and enforces MFA using the SMS Gateway.
- Payment and transfer requests are routed to PayPal and NEFT / RTGS networks respectively.
- All transactions and activities are logged and monitored via Splunk and AppDynamics.

---

## 5. System Boundary and Trust Zones

- **Public Zone:** End‑user devices accessing iNB over the Internet.
- **Application Zone:** iNB web and backend services hosting business logic.
- **Data Zone:** Relational database and object storage containing transactional and KYC data.
- **Third‑Party Zone:** External services including IdP, payment gateways, biller APIs, and notification providers.
- **Operations Zone:** Monitoring, logging, and operational tooling.

---

## 6. Assumptions and Constraints

- All sensitive data is encrypted in transit and at rest.
- Availability target is **99.95%** with defined **RTO** and **RPO**.
- Data residency is restricted to **India** as per regulatory requirements.
- Fraud detection approach will be refined during detailed design.

---

## 7. Next Steps

Proceed to **C4 Level 2 (Container Diagram)** to define the internal structure of the iNB system, including major application components, services, data stores, and technology responsibilities. This will be followed by threat modeling and high‑level architecture definition.

---

# Appendix A: C4 – Level 2 Container Diagram

The following diagram provides the **C4 Level 2 (Container)** view for the iNB Online Banking Application. It shows the major runtime containers, data stores, and key external integrations.

> **Note:** The original Word document references an embedded diagram image. If you want, upload/export the diagram image separately (PNG/SVG), and I can update this Markdown to embed it properly for GitHub rendering.

<!-- Diagram placeholder (image was embedded in the .docx) -->

---
![C4 Level 2 Container Diagram](SystemContext_Diagram.png)
# Appendix B: C4 Level 2 – Container Descriptions

## Web UI (Responsive)
Customer‑facing web application hosted on IIS. Enables registration, login (triggering MFA), dashboard views, statements, bill payments, money transfers, and cheque tracking. Communicates securely with the API Layer over HTTPS.

## Staff / Admin Portal
Internal web application used by bank staff and auditors. Supports registration approvals, cheque status updates, reconciliation reporting, and administrative configuration. All access is role‑based and fully auditable.

## API Layer / Backend
Central backend entry point exposing REST APIs. Orchestrates requests from UI channels, enforces authorization, routes calls to domain services, and applies cross‑cutting concerns such as validation, logging, error handling, and throttling.

## Auth & Session Module
Manages authentication, authorization, MFA orchestration, and session lifecycle. Integrates with the bank’s Identity Provider (SAML) and OTP services and enforces security controls such as account lockout and session timeout.

## Account Service
Handles account data including balances, customer‑specific limits, overdraft eligibility, and interest calculations. Acts as the system‑of‑record interface for account operations and emits events for notifications.

## Statement & Export Service
Generates account statements containing posted transactions only. Supports date‑range queries and export formats such as PDF and Excel. Persists generated artifacts to object storage when required.

## Cheque Deposit Service
Manages cheque deposit requests and lifecycle states (received, in clearance, cleared, bounced). Enforces configurable SLAs and penalties and triggers SMS notifications on status changes.

## Bill Payment Service
Processes immediate and recurring bill payments. Integrates with the bank’s biller APIs and the configured payment gateway (PayPal) and ensures real‑time confirmation to customers.

## Transfer Service
Executes internal and external money transfers (NEFT/RTGS). Enforces per‑customer and per‑transaction limits, invokes fraud checks, and ensures transactional integrity and confirmation.

## Notification Service
Centralized outbound communication service. Sends SMS and Email notifications triggered by business events such as registration approval, cheque status change, overdraft usage, and transaction confirmation.

## Reporting Service (Reconciliation)
Generates daily and monthly reconciliation reports for bank staff and auditors. Supports export in PDF and Excel formats and aligns with audit requirements.

## Configuration Service
Allows authorized administrators to manage business configurations such as interest rates, transaction limits, overdraft rules, SLAs, and penalties. All changes are validated, versioned, and audited.

## Audit Log Collector
Captures immutable audit records for security‑sensitive and business‑critical actions across the platform. Ensures tamper resistance, controlled access, and retention aligned with compliance mandates.

## Observability Agent
Collects application logs, metrics, and traces and forwards them to Splunk and AppDynamics. Enables real‑time monitoring, alerting, and production troubleshooting.

## Relational Database
Primary system of record storing customers, accounts, transactions, configurations, and reconciliation data using a relational data model to guarantee consistency and integrity.

## Object Storage
Stores unstructured data such as KYC documents and exported statements. Compliant with FRRS and jurisdictional retention requirements (minimum 10 years).
