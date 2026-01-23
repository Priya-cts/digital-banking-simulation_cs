**Requirement Analysis and Clarification - Online Banking Application**

**_Application: Indian Net Bank - A Banking Project (Case Study)_**

Table of Contents

[1\. Document History 3](#_Toc219975764)

[1\. Introduction 4](#_Toc219975765)

[2\. Actors 4](#_Toc219975766)

[3\. Use Cases - Functional requirements 4](#_Toc219975767)

[UC1: Customer Registration 4](#_Toc219975768)

[UC2: Login & Home Page 5](#_Toc219975769)

[UC3: Account Management (Savings & Current) 5](#_Toc219975770)

[UC4: Statements 5](#_Toc219975771)

[UC5: Cheque Deposits 5](#_Toc219975772)

[UC6: Reconciliation Report 6](#_Toc219975773)

[UC7: Bill Payments 6](#_Toc219975774)

[UC8: Money Transfer 6](#_Toc219975775)

[UC9: Security, Audit & Compliance 6](#_Toc219975776)

[3.1 Use-Case Diagram 7](#_Toc219975777)

[4\. Use-case - Non-Functional requirements 7](#_Toc219975778)

[UC-NFR1: Authentication & Session Security 7](#_Toc219975779)

[UC-NFR2: Data Protection & Encryption 8](#_Toc219975780)

[UC-NFR3: Audit Logging & Audit Trail Management 8](#_Toc219975781)

[UC-NFR4: Fraud Detection & Risk Control 8](#_Toc219975782)

[UC-NFR5: Performance & Transaction Processing 8](#_Toc219975783)

[UC-NFR6: Availability & Disaster Recovery 9](#_Toc219975784)

[UC-NFR7: Data Backup & Retention Management 9](#_Toc219975785)

[UC-NFR8: Compliance, Privacy & Data Governance 9](#_Toc219975786)

[UC-NFR9: Observability & System Monitoring 9](#_Toc219975787)

[UC-NFR10: Internationalization & Accessibility 10](#_Toc219975788)

[4.1 Use-case Diagram - Non-functional requirements 10](#_Toc219975789)

[8\. Clarification Questions 11](#_Toc219975790)

[Functional 11](#_Toc219975791)

[Non-Functional 13](#_Toc219975792)

[9\. Success Criteria and Milestones 15](#_Toc219975793)

[10\. Future roadmap 16](#_Toc219975794)

[11\. Appendix 16](#_Toc219975795)

# Document History

| **Version No.** | **Date** | **Author** | **Status** | **Comments** |
| --- | --- | --- | --- | --- |
| 1.0 | 20-Jan-2026 | Uttam Bhatia | Draft |     |
|     |     |     |     |     |

- 1. Contributors

| **Contributor** | **Role** |
| --- | --- |
| Omprakash Pandey | Product Manager |
| Manish Dubey | Technical Lead |
| Uttam Bhatia | Architecture & design + requirements |
| Navhin | Security, Compliance and Governance |
| Sandhya | Back-end & DBA role |
| Vishal | Requirement gathering & UI UX |
| Saipriya | Design diagrams |
| Sudhir | Infra & reporting |

- 1. Reviewer / Signoff

| **Reviewer** | **Date of Signoff** | **Document version** |
| --- | --- | --- |
| Omprakash/Manish |     |     |
|     |     |     |
|     |     |     |

# 1\. Introduction

This document presents the requirement analysis for the Online Banking Application. It outlines the key actors, use cases, functional and non-functional requirements, and areas requiring clarification. The purpose is to ensure a shared understanding between business stakeholders and the technical team before proceeding to architecture and implementation. The case-study document is attached in Appendix section.

# 2\. Actors

• Customer (End User): Registers, logs in, performs banking operations.

• Bank Staff (Approver/Admin): Approves registrations, manages cheque clearance, configures limits.

• System (iNB Application): Provides UI, processes transactions, enforces rules.

# 3\. Use Cases - Functional requirements

## UC1: Customer Registration

- Actors: Customer, Bank Staff, System
- Preconditions: Customer has access to iNB portal
- Main Flow:
  - Customer submits registration form with PAN and Aadhaar
  - System performs automated validation
  - Request sent to bank staff for approval with manual override
  - Account created after approval
  - Notification sent via Email and SMS
- Clarifications Applied:
  - Automated approval workflow
  - Mandatory KYC
  - Email and SMS notifications

## UC2: Login & Home Page

- Actors: Customer, System
- Preconditions: Customer account is approved
- Main Flow:
  - Customer enters username and password
  - System triggers MFA verification
  - On success, home dashboard is displayed
- Clarifications Applied:
  - MFA mandatory
  - Dashboard shows balance, last 5 transactions, alerts
  - Account locked after 3 failed attempts

## UC3: Account Management (Savings & Current)

- Actors: Customer, Bank Staff, System
- Preconditions: Customer is logged in
- Main Flow:
  - Savings account rules enforced per customer
  - Current account overdraft applied based on eligibility
  - Daily interest calculated
  - Email notification sent for overdraft usage

## UC4: Statements

- Actors: Customer, System
- Preconditions: Customer is logged in
- Main Flow:
  - Customer requests mini or detailed statement
  - System generates statement with posted transactions only
  - Customer downloads PDF or Excel
- Clarifications Applied:
  - Pending transactions excluded
  - PDF and Excel export supported

## UC5: Cheque Deposits

- Actors: Customer, Bank Staff, System
- Preconditions: Customer has cheque for deposit
- Main Flow:
  - Customer submits cheque deposit request
  - Bank staff updates cheque status
  - System notifies customer via SMS
- Clarifications Applied:
  - Configurable clearance SLA
  - Branch-level bounce penalties

## UC6: Reconciliation Report

- Actors: Bank Staff, Auditors, System
- Preconditions: Authorized staff logged in
- Main Flow:
  - Staff generates daily or monthly report
  - Report exported in PDF or Excel
- Clarifications Applied:
  - Reports consumed by staff and auditors

## UC7: Bill Payments

- Actors: Customer, System
- Preconditions: Customer is logged in
- Main Flow:
  - Customer selects biller and payment mode
  - Immediate or recurring payment scheduled
  - System processes payment in real time and confirms
- Clarifications Applied:
  - Recurring monthly payments supported
  - Real-time confirmation mandatory

## UC8: Money Transfer

- Actors: Customer, System
- Preconditions: Customer is logged in
- Main Flow:
  - Customer enters beneficiary and amount
  - System validates transaction limits and MFA
  - Funds transferred internally or via NEFT/RTGS
  - Confirmation displayed to customer
- Clarifications Applied:
  - Per-customer transaction limits
  - PayPal payment gateway

## UC9: Security, Audit & Compliance

- Actors: Customer, Bank Staff, System
- Preconditions: User accesses sensitive system functions
- Main Flow:
  - System enforces MFA
  - Transactions encrypted in transit and at rest
  - Audit logs captured for user and admin actions
- Clarifications Applied:
  - AES-256 at rest; TLS 1.3 in transit
  - Logs retained for 90 days
  - SOX, and Legal Hold compliance

# Use-Case Diagram

\[Use-Case Diagram Placeholder: UML diagram showing Customer, Bank Staff, and System interacting with UC1-UC9\]
![](useCaseDigrams_F.png)


# 4\. Use-case - Non-Functional requirements

## UC-NFR1: Authentication & Session Security

- Actors: Customer, Bank Staff, System
- Preconditions: User attempts to access the iNB application
- Main Flow:
  - User provides username and password
  - System enforces multi-factor authentication (SMS OTP or authenticator)
  - Session is created with role-based access controls
  - System continuously validates session activity
- Clarifications Applied:
  - MFA is mandatory for all users
  - Account is locked after 3 invalid login attempts
  - Session timeout and re-authentication enforced

## UC-NFR2: Data Protection & Encryption

- Actors: System
- Preconditions: Sensitive customer or transaction data is processed
- Main Flow:
  - System encrypts data before persisting to storage
  - Data in transit is protected using secure communication channels
  - Encryption keys are securely managed
- Clarifications Applied:
  - AES-256 encryption for data at rest
  - TLS 1.3 for data in transit

## UC-NFR3: Audit Logging & Audit Trail Management

- Actors: Customer, Bank Staff, Auditor, System
- Preconditions: A user or admin action is performed in the system
- Main Flow:
  - System captures user and admin activity details
  - Logs are stored securely with access control
  - Authorized users retrieve audit logs for review
- Clarifications Applied:
  - Full audit trail for security and compliance
  - Log retention period is 90 days
  - Role-based access for audit logs

## UC-NFR4: Fraud Detection & Risk Control

- Actors: System, Bank Staff
- Preconditions: A financial transaction is initiated
- Main Flow:
  - System evaluates transaction against fraud rules
  - Suspicious transactions are flagged
  - Alerts are generated for bank staff review
- Clarifications Applied:
  - Fraud detection is mandatory for all transactions

## UC-NFR5: Performance & Transaction Processing

- Actors: Customer, System
- Preconditions: User initiates a transaction or payment
- Main Flow:
  - System processes critical transactions in real time
  - Non-critical tasks are processed in batch
  - User receives real-time confirmation
- Clarifications Applied:
  - Real-time processing for transfers and bill payments

## UC-NFR6: Availability & Disaster Recovery

- Actors: System, IT Operations
- Preconditions: Production environment is operational
- Main Flow:
  - System runs in high-availability mode
  - Secondary system remains on standby
  - Failover is triggered during disaster scenarios
- Clarifications Applied:
  - Availability target 99.95%
  - RTO: 30 minutes
  - RPO: 10 minutes

## UC-NFR7: Data Backup & Retention Management

- Actors: System, Bank Admin
- Preconditions: System processes business and audit data
- Main Flow:
  - Daily backups are executed
  - Backup data is securely stored
  - Retention policy is enforced
- Clarifications Applied:
  - Daily backups scheduled
  - Transaction data retained for 3 years
  - Unstructured data retained for minimum 10 years

## UC-NFR8: Compliance, Privacy & Data Governance

- Actors: System, Compliance Officer
- Preconditions: System handles regulated customer data
- Main Flow:
  - System applies data masking for PII
  - Data residency policies are enforced
  - Legal hold and compliance rules are applied
- Clarifications Applied:
  - SOX and Legal Hold compliance
  - GDPR compliance for APAC and Europe

## UC-NFR9: Observability & System Monitoring

- Actors: System, Application Support Team
- Preconditions: Application is live in production
- Main Flow:
  - System emits logs and metrics
  - Monitoring tools capture performance data
  - Alerts are generated for system anomalies
- Clarifications Applied:
  - Integration with Splunk and AppDynamics

## UC-NFR10: Internationalization & Accessibility

- Actors: Customer, System
- Preconditions: User accesses application from supported region
- Main Flow:
  - User selects preferred language
  - System renders UI accordingly
  - Accessibility standards are applied
- Clarifications Applied:
  - Support for English and French languages
  - Responsive and accessible UI across devices

# 4.1 Use-case Diagram - Non-functional requirements
![](useCaseDiagrams_NF.png)


# 8\. Clarification Questions

## Functional

| **#** | **Requirement Area** | **Question** | **Response (Example)** | **Stakeholder** | **Status** |
| --- | --- | --- | --- | --- | --- |
|     | Registration | Should approval be manual only or automated workflow? | Automated with manual override | Product Owner | Confirmed |
|     | Registration | Notification method (postal letter, email, SMS)? | Email and SMS preferred | Product Owner | Confirmed |
|     | Registration | Any KYC/document upload required? | Yes, PAN and Aadhaar mandatory | Compliance Lead | Confirmed |
|     | Login & Home Page | Should home page show only balance or also recent transactions, alerts? | Include balance, last 5 transactions, alerts | UX Designer | Confirmed |
|     | Account Management | Are withdrawal limits/min balance global or per-customer configurable? | Configurable per customer | Product Owner | Confirmed |
|     | Account Management | Should overdraft charges trigger daily notifications? | Yes, via email | Product Owner | Confirmed |
|     | Account Management | Interest rate changes - who manages configuration? | Bank Admin via dashboard | Admin Team | Confirmed |
|     | Account Management | Overdraft limit rules (eligibility criteria, Interest rate and grace period) |     |     | Pending |
|     | Statements | Should statements include pending transactions? | No, only posted transactions | Finance Team | Confirmed |
|     | Cheque Deposits | Bounced cheque fine - fixed or configurable? | Configurable by branch | Operation Lead | Confirmed |
|     | Cheque Deposits | SLA for cheque clearance (always 3 days or configurable)? | Configurable (default 3 days) | Operation Lead | Confirmed |
|     | Reports | Who consumes reports (staff, auditors, customers)? | Staff and auditors | Audit Team | Confirmed |
|     | Reports | Frequency of report generation? | Daily and monthly | Audit Team | Confirmed |
|     | Bill Payments | Should recurring payments be supported? | Yes, monthly scheduling | Product Owner | Confirmed |
|     | Money Transfer/Transactions | Should transfers to external banks be supported? | Yes, via NEFT/RTGS | Tech Lead | Confirmed |
|     | Money Transfer/Transactions | Transaction limits configurable? | Yes, per customer and per transaction type | Admin Team | Confirmed |
|     | Money Transfer/Transactions | Type of transactions supported **initially** (Deposit, Withdrawal, transfer) | Money Transfer | Product Owner | Confirmed |
|     | Money Transfer/Transactions | Preferred payment gateway if any? | PayPal | Product Owner | Confirmed |
|     | Good to have - Additional features/services | Additional features/services i.e. FD, RD, Instant Loans etc. | Out of scope | Product Owner | Confirmed |
|     | Reports/Statements | Export options (PDF, Excel)? | PDF, Excel | Product Owner | Confirmed |
|     | Notifications | Should customers be notified of status changes of cheque deposits via email/SMS? | Preferred via SMS. | Product Owner | Confirmed |
|     | Integration | Integration with external biller APIs? | Preferred to use Bank's existing Biller APIs | Product Owner | Confirmed |
|     | Data model | What type of data model should the system use | Relational | Product Owner | Confirmed |
|     | Object Storage | Storage for Unstructured data | FRRS compliant with Jurisdiction and retention period of min 10 years | Compliance and Security Lead | Confirmed |
|     | Data retention period | Retention period for transactional data |     | 3 years | Confirmed |

## Non-Functional

| **#** | **Requirement Area** | **Question** | **Response (Example)** | **Stakeholder** | **Status** |
| --- | --- | --- | --- | --- | --- |
|     | Security | Should login support multi-factor authentication? | Yes, via SMS OTP or authenticator app | Security Lead | Confirmed |
|     | Security | User's authorization and session policy - preferred identity provider if any? | Preferred bank's existing IdProvider service i.e. SAML | Security Lead | Confirmed |
|     | Security | Encryption standards for data at rest/in transit? | AES-256 at rest, TLS 1.3 in transit | Security Lead | Confirmed |
|     | Security | Audit logging requirements? | Full audit trail with role-based access | Compliance Lead | Confirmed |
|     | Security | User fraud-detection? |     | Security Lead | Confirmed |
|     | Performance | Real-time vs batch processing? | Real-time for critical, batch for non-critical | Tech Lead | Confirmed |
|     | Performance | Real-time confirmation required? | Yes, for transfers and bill payments | Product Owner | Confirmed |
|     | Availability | Application uptime?<br><br>Disaster recovery - RTO, RPO | Availability 99.95%<br><br>RTO - 60 mins, RPO-10 mins | Production | Confirmed |
|     | Reliability | Automatic failover/recovery | Secondary system in standby mode - Manual failover/recover | SRE/Infra Tech Lead | Confirmed |
|     | Latency | User Latency requirement - Critical/High/Low/Medium | High latency - introduce Caching framework and call optimizations. | Infra Tech team | Confirmed |
|     | Scalability | Volume of transactions supported - peak concurrent transactions? | ~10k transactions daily | Operation Lead | Confirmed |
|     | Usability | Mobile/responsive design required? | Yes, responsive across devices | UX Designer | Confirmed |
|     | Compliance | Any compliance & Legal hold requirement i.e. SOX, Legal hold requirement, RBI etc. | SOX and LH Requirement | Compliance Lead | Confirmed |
|     | Compliance | Data residency compliance i.e. RBI Compliant etc. | India | Compliance Lead | Confirmed |
|     | Compliance | User consent and privacy policy | Data masking and PII | Compliance Lead | Confirmed |
|     | Compliance | Data backup & application log retention and access policy | Data backup required, Application log retention of 90 days | Compliance Lead | Confirmed |
|     | Compliance | Data backup frequency? | Daily, Nightly Job | Product Owner | Confirmed |
|     | I18n | Multi-lingual supports? | Yes, Support for English & Hindi | Product Owner | Confirmed |
|     | Observability | Metrics, tracing, monitoring, System health, and alerting - integration with Splunk, AppDynamics etc. | Yes, Integration of Log tracing and system monitoring tools required | Application Support Team | Confirmed |
|     | Maintainability | Configurability, testability |     | Tech Lead | Confirmed |
|     | Maintainability | Portability, and Modularity | **Nice to have** | DevOps | Pending |

# 9\. Success Criteria and Milestones

- How will success be measured?
- Key KPIs after go-live?
- Go-Live timelines and milestones?

# 10\. Future roadmap

- Cloud migration - Hybrid

# 11\. Appendix

![](data:image/x-emf;base64,AQAAAGwAAAABAAAAAQAAAJQAAABbAAAAAAAAAAAAAAB7CQAAMQYAACBFTUYAAAEAeCoAABUAAAACAAAAAAAAAAAAAAAAAAAAgAcAADgEAAA1AQAArgAAAAAAAAAAAAAAAAAAAAi3BACwpwIAGAAAAAwAAAAAAAAAGQAAAAwAAAD///8AcgAAAKAkAAAzAAAAAQAAAGIAAAAwAAAAMwAAAAEAAAAwAAAAMAAAAACA/wEAAAAAAAAAAAAAgD8AAAAAAAAAAAAAgD8AAAAAAAAAAP///wAAAAAAbAAAADQAAACgAAAAACQAADAAAAAwAAAAKAAAADAAAAAwAAAAAQAgAAMAAAAAJAAAAAAAAAAAAAAAAAAAAAAAAAAA/wAA/wAA/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAQIiu+xAAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/xsmub4AAR4gAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEBAEEe71AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8EEe71AAEeIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAInKAAD/r/AA/6/3qC+P+4u/b/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/Hyvb/mZ73/w4d+v8AD/r/AAmssAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/iZD3//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/5me9/8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/5uf1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/8fK9v8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/LCws/7+/v//19fX/9fX1//X19f85OTn/LCws/0dHR/+Xl5f/9fX1/7Kysv8sLCz/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/LCws/7+/v//19fX/9fX1//X19f8sLCz/l5eX/7Kysv85OTn/l5eX/6Wlpf8sLCz/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/LCws/ywsLP9HR0f/paWl//X19f8sLCz/paWl//X19f+Kior/R0dH/6Wlpf8sLCz/v7+//7+/v//19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/LCws/5eXl/+Xl5f/LCws/9ra2v8sLCz/paWl//X19f+lpaX/LCws/6Wlpf8sLCz/LCws/ywsLP/a2tr/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/LCws/7+/v//19fX/LCws/7Kysv8sLCz/paWl//X19f+Xl5f/OTk5/6Wlpf8sLCz/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/LCws/7Kysv+lpaX/LCws/83Nzf8sLCz/l5eX/7+/v/85OTn/ioqK/6Wlpf8sLCz/v7+//7+/v//o6Oj/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/OTk5/ywsLP9HR0f/l5eX//X19f85OTn/LCws/0dHR/+Kior/9fX1/7Kysv8sLCz/LCws/ywsLP+ysrL/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/6it9/96gvj/mZ73/+bn9f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/x8r2/wAP+v8eK/n/AA/6/y05+f/m5/X/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/x8r2/wAP+v+4u/b/W2X4/wAP+v8OHfr/5uf1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/0xW+P8OHfr/qK33/0xW+P8AD/r/PUj5//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f9rc/j/AA/6/y05+f8AD/r/AA/6/4mQ9//19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/7i79v+4u/b/1tj2//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/x8r2/0xW+P8AD/r/AA/6/w4d+v+orff/9fX1//X19f/19fX/9fX1/+bn9f9rc/j/Dh36/wAP+v8AD/r/AA/6/3qC+P/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/W2Pb/Dh36/wAP+v8AD/r/AA/6/z1I+f96gvj/qK33/x4r+f8OHfr/eoL4/+bn9f9rc/j/AA/6/z1I+f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/eoL4/wAP+v8AD/r/a3P4/z1I+f8AD/r/AA/6/wAP+v8tOfn/PUj5/z1I+f8AD/r/Dh36/6it9//19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/5uf1/w4d+v8AD/r/eoL4/8fK9v8OHfr/LTn5/7i79v96gvj/eoL4/3qC+P+Znvf/5uf1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/1tl+P8AD/r/Hiv5/y05+f8OHfr/x8r2//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/8fK9v8AD/r/AA/6/wAP+v+orff/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8tOfn/AA/6/y05+f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f9bZfj/AA/6/x4r+f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8OHfr/AA/6/wAP+v/W2Pb/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/7i79v8AD/r/PUj5/wAP+v+orff/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/3qC+P89SPn/eoL4/wAP+v96gvj/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/3qC+P89SPn/uLv2/wAP+v+orff/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/9bY9v8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/7i79v8OHfr/TFb4/x4r+f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/3qC+P8AD/r/AA/6/wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/+bn9f9MVvj/Dh36/4mQ9//19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/5uf1/w4d+v8AD/r/AAq6wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/m5/X/LTn5/wAP+v8AD/r/AAIuMAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f89SPn/AA/6/wAM6fAABE1QAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/1tl+P8AD/r/AAzp8AACLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/W2X4/wAP+v8ADOnwAAIuMAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAP+v8AD/r/x8r2//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f9bZfj/AA/6/wAM6fAAAi4wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAL2+AAD/r/TFb4//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1/3qC+P8AD/r/AAzp8AACLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHfIAAD/r/AA/6/2tz+P/W2Pb/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f/19fX/9fX1//X19f+Plez2JDDi6AAP+v8ADOnwAAIuMAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACsvQAA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAM6fAAAi4wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAd8gAAM6fAAD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AA/6/wAP+v8AD/r/AAq6wAACLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABgAAAAMAAAAAAAAAhIAAAAMAAAAAQAAAFIAAABwAQAAAQAAABQAAAAAAAAAAAAAAAAAAAC8AgAAAAAAAAECAiJTAHkAcwB0AGUAbQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAGR2AAgAAAAAJQAAAAwAAAABAAAAJQAAAAwAAAANAACAKAAAAAwAAAABAAAAUgAAAHABAAABAAAA8P///wAAAAAAAAAAAAAAAJABAAAAAAAAAEAAIlMAZQBnAG8AZQAgAFUASQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAZHYACAAAAAAlAAAADAAAAAEAAABUAAAAtAAAAAEAAAAyAAAAlAAAAEYAAAABAAAAAMCAQY7jgEEBAAAAMgAAABEAAABMAAAABAAAAAAAAAAAAAAAlgAAAGIAAABwAAAAMQA3ADYANwA4ADQAMwA1ADkANgAxADMANwAtAEMAYQBzAAAACQAAAAkAAAAJAAAACQAAAAkAAAAJAAAACQAAAAkAAAAJAAAACQAAAAkAAAAJAAAACQAAAAYAAAAKAAAACAAAAAcAAABUAAAAqAAAABMAAABHAAAAgQAAAFsAAAABAAAAAMCAQY7jgEETAAAARwAAAA8AAABMAAAABAAAAAAAAAAAAAAAlgAAAGIAAABsAAAAZQAtAFMAdAB1AGQAeQAtAGkATgBCAC4AcABkAGYAAAAIAAAABgAAAAkAAAAFAAAACQAAAAkAAAAIAAAABgAAAAQAAAAMAAAACQAAAAMAAAAJAAAACQAAAAUAAAAlAAAADAAAAA0AAIBGAAAAIAAAABIAAABJAGMAbwBuAE8AbgBsAHkAAAAAAEYAAABQAAAAQgAAADEANwA2ADcAOAA0ADMANQA5ADYAMQAzADcALQBDAGEAcwBlAC0AUwB0AHUAZAB5AC0AaQBOAEIALgBwAGQAZgAAAAAARgAAABAAAAACAAAAAAAAAEYAAAAQAAAABAAAAIwAAABGAAAAIAAAABIAAABJAGMAbwBuAE8AbgBsAHkAAAAAAA4AAAAUAAAAAAAAABAAAAAUAAAA)
