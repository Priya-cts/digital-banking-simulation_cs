# High‑Level Deployment Model Architecture (UML) – iNB Online Banking

This document provides the high‑level deployment model architecture for the Indian Net Bank (iNB) Online Banking application, including UML deployment diagrams, an environment matrix, a production vs DR (Active‑Passive) view, backup/restore flow, and network segmentation with firewall/security zones. It is derived from the attached requirements, C4 context/container model, HLD/DFDs/security architecture, architectural guardrails, and RTM/STRIDE mappings.

## 1\. Confirmed Deployment Inputs

• Hosting: On‑premise (India data centers; RBI-aligned data residency)

• Runtime: Virtual Machines (VMs) with horizontal scaling at Web/API/service tiers

• Database: Oracle (clustered for HA in Production)

• DR pattern: Active‑Passive (warm standby) with manual failover

## 2\. Requirement‑Driven Deployment Considerations (Highlights)

• Security: MFA mandatory for all users; SAML-based Identity Provider integration; account lockout after 3 failed attempts; step-up MFA for high-risk actions.

• Cryptography: TLS 1.3 in transit for all hops; AES‑256 at rest for Oracle, object storage, and backups; centralized key management via enterprise KMS/HSM.

• Audit & compliance: immutable audit trail for login/MFA/session and money-moving actions; minimum 90‑day online log retention for application logs; SOX + Legal Hold readiness.

• Availability & DR: 99.95% availability target; RTO 30 minutes; RPO 10 minutes; periodic DR drills with evidence.

• Data governance: transactional retention ~3 years; unstructured/KYC retention minimum 10 years; India-only data residency.

• Observability: Splunk for centralized logs/SIEM; AppDynamics for APM/metrics/tracing; alerting on anomalies and SLA breaches.

## 3\. Network Segmentation, Firewalls, and Security Zones

The on‑premise deployment uses layered network segmentation with dedicated security zones and firewall enforcement to support zero‑trust guardrails, compliance, and auditability.

Security zones (logical):

• Internet/Public: Untrusted client networks (customers, staff over Internet/VPN).

• DMZ / Edge Zone: Internet-facing controls such as WAF, reverse proxy, and external load balancers. No direct access to data zone.

• Application Zone: VM pools hosting Web UI, Staff/Admin Portal, API layer, Auth & Session, and domain services.

• Data Zone: Oracle DB cluster, object storage/FRRS archives, and backup repositories; strictly restricted inbound.

• Operations / Management Zone: Jump hosts/bastion, administration tools, patching, monitoring collectors; privileged access controlled and audited.

• Shared Services / Integration Egress: Controlled outbound integration endpoints, DNS/NTP/PKI, and optional SMTP relay; egress allow‑lists.

Firewall layers (recommended):

• Perimeter Firewall: Internet ↔ DMZ; enforces inbound policy and edge exposure constraints.

• Internal Segmentation Firewall (ISFW): DMZ ↔ Application/Data; enforces east‑west controls and default‑deny rules.

• Egress Firewall: Application/Shared Services ↔ External Systems; enforces strict allow‑lists and outbound rate limits.

Typical allowed flows (high‑level):

• Internet → DMZ: HTTPS 443 to WAF/reverse proxy only.

• DMZ → Application Zone: HTTPS 443 to Web/API endpoints; no direct DMZ → Data Zone.

• Application Zone → Data Zone: Oracle DB access (e.g., listener/SCAN) and object storage APIs; restricted to required hosts only.

• Application Zone → External Systems: HTTPS 443 only to approved endpoints via egress allow‑lists.

• Ops/Management Zone → Application/Data: Admin access only via bastion/jump hosts with strong MFA and audited sessions.

• Telemetry: Agents/forwarders → Splunk/AppDynamics over secured channels; PII masking in logs enforced.

Addressing/VLAN note: Use dedicated VLANs/subnets per zone (DMZ/App/Data/Ops) with non‑overlapping CIDRs. Exact CIDRs/VLAN IDs should follow enterprise network standards and are intentionally not fixed in this high‑level model.

### 3.1 Network Segmentation Diagram (Zones & Firewalls)

Conceptual segmentation view showing security zones and firewall boundaries.

![](diagrams/DM_NW_Seg.png)

### 3.2 UML Deployment View (Production – with Firewalls)

UML deployment diagram updated to include firewall devices and zone boundaries.

![](diagrams/DM_ProdDep.png)

## 4\. UML Deployment Diagram – Production

High-level UML deployment view of production topology with key nodes and trust boundaries.

![](diagrams/DM_ProdDep.png)

## 5\. Environment Matrix (Dev / QA / UAT / Prod / DR)

Summary of environment characteristics aligned to controls and operational needs.

| Dimension | Dev | QA/Test | UAT | Prod | DR (Standby) |
| --- | --- | --- | --- | --- | --- |
| Purpose | Feature dev & unit tests | Integration/functional/security testing | Business validation & sign‑off | Customer‑facing | Continuity; activated on failover |
| Hosting | On‑prem (India) | On‑prem (India) | On‑prem (India) | On‑prem (India) | On‑prem (India) |
| Compute | VMs (scaled down) | VMs (scaled) | VMs (controlled) | VM pools (HA) | VM pools (warm standby) |
| DB | Oracle dev instance | Oracle test instance | Oracle UAT instance | Oracle cluster (primary) | Oracle standby (Data Guard) |
| Data | Synthetic/masked | Masked + test data | Masked/subset (approved) | Real customer data | Replicated prod data |
| TLS | TLS 1.3 | TLS 1.3 | TLS 1.3 | TLS 1.3 | TLS 1.3 |
| At‑rest crypto | AES‑256 recommended | AES‑256 required | AES‑256 required | AES‑256 required | AES‑256 required |
| Auth | SAML optional/mock; MFA for privileged | SAML + MFA enabled | SAML + MFA enabled | SAML + MFA mandatory | SAML + MFA mandatory |
| Logging | Enabled (non‑prod retention) | Enabled; test evidence | Enabled; sign‑off evidence | Immutable; 90‑day online min | Immutable; synchronized |
| Observability | Basic dashboards | Full telemetry | Full telemetry | Splunk + AppDynamics | Splunk + AppDynamics (DR) |
| Backup/Restore | Optional snapshots | Scheduled + restore tests | Scheduled + restore tests | Nightly + restore drills | Backup copy + DR drills |

## 6\. UML Deployment Diagram – Production vs DR (Side‑by‑Side)

Active‑Passive DR view showing warm standby, replication, and RTO/RPO targets.

![](diagrams/DM_ProdDR.png)

## 7\. Backup and Restore Flow (UML Activity)

Nightly backup, encrypted repositories, DR copy, and periodic restore validation.

![](diagrams/DM_Bkp_Restore.png)

## 8\. Inputs Needed to Finalize Segmentation (If Available)

• Enterprise network standards for VLAN IDs and CIDR ranges per zone (DMZ/App/Data/Ops).

• Firewall products and policy constraints (e.g., TLS inspection allowed/not allowed).

• Whether staff/admin access is via corporate VPN, ZTNA, or dedicated intranet.

• Oracle connectivity standard (single listener vs SCAN; port standardization).

• Whether micro‑segmentation (host-based) is required in addition to ISFW.
