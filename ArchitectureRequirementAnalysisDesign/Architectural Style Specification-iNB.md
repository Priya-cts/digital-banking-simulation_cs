# Architectural Style Specification – iNB Online Banking Application

## 1\. Purpose of Document

This document formally defines the architectural style(s) adopted for the Indian Net Bank (iNB) Online Banking Application. It is a standalone architectural artifact intended for Architecture Review Board (ARB), Security, Compliance, Engineering, Operations, and Audit stakeholders.

This artifact is derived from Architectural Artifacts Set 1 (Architecture Baseline), Set 2 (Logical & Service Design), and Set 3 (Data Architecture).

## 2\. Architectural Drivers

The architectural style decisions for iNB are driven by the following critical concerns:

• Regulatory compliance (RBI, SOX, Legal Hold, India data residency)

• Strong transactional consistency for financial operations

• Comprehensive auditability and non‑repudiation

• Predictable operations, recoverability, and incident response

• Scalability with controlled system complexity

• Long‑term maintainability and extensibility

## 3\. Primary Architectural Style – Layered Architecture

The iNB system adopts a classic Layered (N‑Tier) Architectural Style as its foundational structure. Responsibilities are clearly separated into Presentation, API/Orchestration, Domain Services, Platform & Cross‑Cutting, and Data layers.

This separation enables controlled dependency direction, stronger security enforcement, simplified testing, and easier regulatory inspection.

## 4\. Supporting Architectural Style – Service‑Oriented Architecture (SOA)

Within the layered structure, iNB follows a Service‑Oriented Architecture (SOA) model. Business capabilities are implemented as domain‑aligned services with explicit responsibilities.

SOA enables reuse, centralized governance, shared security controls, and standardized integration without requiring microservice‑level operational overhead.

## 5\. Modular Monolith Pattern

Operationally, the system follows a Modular Monolith pattern. Domain services are logically isolated and internally API‑driven but deployed within a controlled set of runtimes.

This pattern ensures strong consistency, simpler operations, and regulatory compliance while preserving future decomposition paths.

## 6\. Transactional (ACID‑Centric) Architecture

iNB is designed as an ACID‑first transactional system. Financial and ledger operations rely on Oracle RDBMS as the authoritative system of record.

Eventual consistency approaches are intentionally avoided for money‑moving flows to preserve correctness, reconciliation accuracy, and audit integrity.

## 7\. Selective Event‑Driven Architecture

Event‑driven patterns are selectively applied for non‑transactional concerns such as notifications, fraud alerts, audit streaming, and observability.

Core transactional paths remain synchronous, while downstream consumers process events asynchronously to improve resilience and decoupling.

## 8\. Explicitly Excluded Architectural Styles

The following architectural styles were evaluated and deliberately excluded:

• Pure microservices – due to operational complexity and consistency risks

• Event sourcing – due to unnecessary complexity and overlapping audit needs

• Serverless‑first architecture – due to regulatory, latency, and state constraints

• CQRS with polyglot persistence – due to reconciliation and reporting complexity

## 9\. Architecture Style Summary

The iNB Online Banking Application follows a layered, domain‑driven modular SOA architecture with strong ACID transactional guarantees and selectively applied event‑driven extensions, optimized for a regulated on‑premise banking environment.

## 10\. Governance and Applicability

This architectural style specification is mandatory for all iNB solution components unless an explicit Architecture Decision Record (ADR) is approved by the Architecture Review Board.

Any deviation must be formally assessed for security, compliance, operability, and audit impact prior to implementation.