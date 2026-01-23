equirements & UI/UX design pack you can hand to development for the iNB (Indian Net Bank) Online Banking Application, optimized for a clean transition from Classic ASP to ASP.NET and deployment on IIS. I’ve structured it the way an architect would for discovery → definition → design → delivery, so you can move straight into backlog creation, low‑fi wireframes, and sprint planning.

1) Scope & Objectives
Goal: Build a secure, performant, and consistent online banking application for iNB that supports account opening, daily banking, deposits (cheques), payments, and transfers—ready for IIS deployment, with strong guardrails on security and UX consistency.
Primary modules

Customer Registration & Onboarding
Authentication & Account Lockout
Home & Accounts (balance, mini & detailed statements)
Savings Accounts (interest, min balance, daily withdrawal limits)
Current Accounts (zero balance option, overdraft & daily compounding)
Cheque Deposits (online slip, lifecycle, fines for bounced)
Reconciliation Reporting (as-of-date summary)
Bill Payments (immediate/scheduled)
Money Transfers (within iNB)
Admin/Banker Workbench (approvals, cheque processing, lockout reactivation, parameters)


2) Stakeholders & Roles
Stakeholders

Customers (Retail banking users)
Banker/Operations (Back-office processing)
Admin (Security, configuration, rates, parameters)
Compliance/Audit
Engineering/DevOps (IIS hosting, deployment, monitoring)
Support/Service Desk

User Roles & Permissions

Customer: Register, log in, view accounts/statements, deposit cheques (slip), check cheque statuses, pay bills, schedule payments, transfer money.
Banker/Operations: Review & approve registrations, update cheque statuses, process clearances/bounces, apply fines, run reconciliation report.
Admin: Manage system parameters (interest rates, overdraft rate, daily withdrawal limits, min balance), manage users (unlock accounts), manage payees/billers catalog, oversee audit logs.


3) Personas (for UX Decisions)

Arun, 32, IT Professional

Heavy mobile use, quick tasks, instant bill payments, transfers, mini statements.


Meera, 48, Small Business Owner

Current account; overdraft; needs detailed statements and reconciliation for accounting.


Ravi, 40, Ops Executive (Banker)

Processes cheque slips daily; needs clear status dashboards and exception handling.




4) Functional Requirements (FR)
FR-1 Registration & Onboarding

Customers can submit online registration for Savings or Current accounts.
Registration requires: full name, DOB, mobile, email, PAN, address, preferred account type, KYC document info (numbers & type), initial deposit preference (if applicable).
On submit → Pending Approval. Banker approves/rejects.
On approval → system creates account, generates account number, sends physical letter to the customer’s address (record dispatch date & tracking if available), and sends confirmation email/SMS. Login enabled only after approval/letter dispatched.
If rejected → reason recorded, email/SMS notification.

FR-2 Authentication, Lockout, & Session

Login with username + password (and optional OTP if MFA is enabled in future).
Account lockout after 3 consecutive invalid attempts; only manual reactivation by Admin/Banker.
Session timeout (configurable, default 15 min of inactivity). Explicit logout.

FR-3 Home Page (Post-login)

Display customer summary: name, last login, accounts list with available balance.
Quick actions: Pay Bill, Transfer Money, Deposit Cheque (Slip), View Statement.

FR-4 Accounts & Statements

Types: Savings and Current.
Mini statement: last 5 transactions.
Detailed statement: date range (e.g., up to last 24 months), export to PDF/CSV.
Savings:

Unified interest rate across bank, configurable (Admin).
Daily/monthly interest accrual on balances; credited monthly (end-of-month).
Daily cash withdrawal limit (configurable).
Minimum balance (configurable); if breached, apply penalty (configurable).


Current:

Zero balance option (configurable per account).
Overdraft facility (flag per account; limit per account).
Overdraft interest rate (single, bank-wide, configurable); charged daily on negative balances.



FR-5 Overdraft Charges Calculation

Daily compounding/accumulation on negative EoD balance.
Formula (for day d):
OD_Interest_d = max(0, -Balance_EOD_d) * (OD_Rate_Per_Annum / 365)
At EOD: Balance_EOD_{d+1} = Balance_EOD_d - OD_Interest_d (+/- other postings).

FR-6 Cheque Deposits (Online Slip)

Customer fills online slip: cheque number, amount, issuing bank/branch, date, payee=customer, remarks, and prints slip.
System creates record with status Not Received.
Banker updates status:

Sent for Clearance (when physically received & forwarded)
Cleared (T+3 working days typical; configurable)
Bounced (with reason & fine auto-debited; configurable)


Customer can track all cheque statuses and view associated postings.
Bounced cheques can be Resubmitted (fresh slip).

FR-7 Reconciliation Report

As-of date report listing:

Received cheques count & amount
Cleared count & amount
Bounced count & amount
Not-cleared (in-progress) count & amount


Export to PDF/CSV. Filter by date range, branch (if multi-branch later), and status.

FR-8 Bill Payments

Pay utility bills (electricity, telephone, mobile, etc.).
Immediate payment or Scheduled for a future date.
Manage Saved Billers (add, edit, delete).
Show payment receipts and status (Success/Failed/Pending if external).
Prevent scheduling beyond allowed window (e.g., up to 12 months).

FR-9 Money Transfer (iNB → iNB)

Transfer between the customer’s own accounts and to other iNB accounts.
Add & verify payees (account number + name).
Limits (per transaction/per day) are configurable.
Transfer receipt with reference number; reversal rules per bank policy.

FR-10 Look-and-Feel & Navigation

Consistent header, footer, left navigation across all pages.
Breadcrumbs and 2–3 click access to primary tasks.
Mobile-responsive design.

FR-11 Admin/Banker Capabilities

Approve/Reject registrations.
Unlock accounts (post lockout).
Update global parameters (Savings interest, OD rate, min balance, daily withdrawal limit, bounce fine, statement export window).
Cheque lifecycle ops, reconciliation.
Audit log search & export.


5) Non-Functional Requirements (NFR)

Security: TLS 1.2+, OWASP Top 10 mitigations, parameterized queries, CSRF tokens, XSS sanitization, strong password policy, secure session cookies (HttpOnly, Secure), audit logging.
Performance: P95 page response < 1.5s @ target load; batch interest/OD jobs complete within defined nightly window.
Availability: 99.5%+ (single-region) to start; planned HA in future.
Scalability: Horizontal app scaling behind IIS ARR or Azure App Service (future).
Reliability: ACID on transactional updates; idempotent payment operations.
Observability: Centralized logs, error tracking, request correlation IDs.
Compliance: RBI/Banking best practices (KYC data protection), data retention policies.
Accessibility: WCAG 2.1 AA.
Localization: INR currency, Indian date formats (DD-MMM-YYYY), time zone handling.


6) Information Architecture (IA)
Global Nav (Left panel)

Home
Accounts

Overview
Statements


Payments

Bill Payments
Payees / Billers


Transfers

To Own Accounts
To Other iNB Accounts


Cheques

Deposit Slip
Cheque Status
Reconciliation (Banker)


Admin (role-restricted)

Registrations
Users & Lockouts
Parameters
Audit Logs


Help / Support

Breadcrumbs Example

Home › Accounts › Statements › Detailed


7) Data Model (High-level)
Entities

User(UserId, Name, DOB, Email, Mobile, Address, PAN, Role, Status, CreatedAt)
LoginAccount(UserId FK, Username, PasswordHash, FailedAttempts, LockedAt, LastLoginAt)
Account(AccountId, UserId FK, Type[SAVINGS|CURRENT], Status, OpenedAt, OverdraftEnabled, OverdraftLimit, ZeroBalanceAllowed)
AccountParameters(ParamId, Name, Value, EffectiveFrom, EffectiveTo)
Examples: SavingsInterestRate, OverdraftAnnualRate, MinSavingsBalance, DailyWithdrawalLimit, BounceFine
Transaction(TxnId, AccountId, Type[CREDIT|DEBIT|CHARGE|INTEREST], Amount, Currency, ValueDate, BalanceAfter, Ref, Narration, CreatedAt)
ChequeSlip(SlipId, UserId, AccountId, ChequeNo, Amount, IssuingBank, Branch, ChequeDate, Status[NotReceived|SentForClearance|Cleared|Bounced], StatusDate, BounceReason, PrintedAt)
Biller(BillerId, Name, Category, Active)
CustomerBiller(Id, UserId, BillerId, ConsumerNo, Nickname)
Payment(PaymentId, UserId, FromAccountId, BillerId, Amount, ScheduledFor, Status[Pending|Processing|Success|Failed], Reference)
Payee(PayeeId, UserId, PayeeAccountNo, PayeeName, Verified)
Transfer(TransferId, FromAccountId, ToAccountId, Amount, Status, Reference, CreatedAt)
AuditLog(AuditId, ActorUserId, Action, Entity, EntityId, Before, After, IP, UserAgent, Timestamp)
Registration(RegId, UserId, AccountType, KYCType, KYCNumber, Status[Pending|Approved|Rejected], DecisionBy, DecisionAt, Remarks, LetterDispatchedAt, LetterTrackingNo)

Indexes

Accounts by UserId; Transactions by AccountId+ValueDate; ChequeSlip by Status; AuditLog by Timestamp+Actor.


8) Core Workflows (Swimlane/Sequence – Text)
A) Registration → Approval → Activation

Customer submits registration → Status=Pending.
Banker reviews KYC → Approve/Reject.
If Approved: create Account, generate Account No; dispatch letter; set LoginAccount.Status=Active.
Notify via SMS/Email.

B) Login & Lockout

Customer enters credentials.
If incorrect: FailedAttempts++.
If FailedAttempts==3 → Lock account, show “Your account is locked. Contact support.”
Banker/Admin can unlock (set FailedAttempts=0, LockedAt=NULL).

C) Savings Interest Posting (Batch Job)

Daily accrual or monthly simple interest on EoD balance (per bank policy).
Monthly posting: sum daily accruals → post CREDIT transaction “Savings Interest – MM/YYYY”.

D) Overdraft Charge (Daily)

End of day: if Balance < 0, compute OD_Interest_d.
Post DEBIT transaction “OD Interest – dd/MM”.

E) Cheque Lifecycle

Customer submits & prints slip → Status=NotReceived.
Bank receives physical cheque → Status=SentForClearance.
After T+3 working days:

If cleared → CREDIT account, Status=Cleared.
If bounced → DEBIT BounceFine, Status=Bounced, capture reason.



F) Bill Payment (Immediate/Scheduled)

Create Payment (Pending).
At execution time → debit account; if success → Status=Success; else → Failed & rollback.

G) Money Transfer (iNB → iNB)

Validate From/To accounts, limits, and balance.
Post DEBIT on From; CREDIT on To; create Transfer record with atomicity.


9) Validation Rules (Samples)

Registration: PAN format (AAAAA9999A), mobile (10 digits), email RFC 5322.
Password policy: min 8 chars, 1 upper, 1 lower, 1 number, 1 special.
Date ranges: Detailed statement max 24 months; Cheque date cannot be future-dated > 90 days.
Transfer: Amount > 0; do not exceed per-transaction and daily limits; prevent overdraft unless applicable.
Savings: Enforce min balance on debit; show blocking error if violation (unless charge allowed).
Current: If overdraft enabled, ensure not exceeding OD limit incl. accrued interest.


10) Error Handling & Messaging

Friendly, actionable errors; no sensitive detail leakage.
Examples:

Login: “Incorrect username or password.” / “Account locked after 3 attempts. Please contact support.”
Payments/Transfer: “Insufficient funds. Your available balance is ₹XX,XXX.XX.”
Cheque: “This cheque appears stale-dated. Please verify cheque date.”




11) UI/UX Design (Low‑fi Wireframe Descriptions)
Global Layout

Header: iNB logo (top-left), user name/welcome, last login, logout.
Left Nav: Sections (Accounts, Payments, Transfers, Cheques, Admin).
Footer: © iNB, support contact, version, accessibility link.

Key Screens

Login


Fields: Username, Password
Actions: Sign In; “Forgot password?” (future); “Register” CTA
Security note: “Your session will time out after inactivity.”


Registration


Steps (wizard): Personal Details → KYC → Account Type → Review & Submit
Success screen with reference no. & next steps (“Await approval & physical letter”).


Home


Cards per Account (type, account no. masked, available balance)
Quick Actions: Pay Bill, Transfer, Deposit Cheque, Statement
Alerts: Scheduled payments due, bounced cheque notices.


Accounts › Statement


Tabs: Mini (last 5), Detailed (date picker, filters, export CSV/PDF)
Table columns: Date, Description/Narration, Debit, Credit, Balance, Ref


Cheques › Deposit Slip


Form: Cheque No, Amount, Issuing Bank, Branch, Date, Remarks, Target Account
CTA: Save & Print
Success: PDF slip with barcode/QR (SlipId), print instruction


Cheques › Status


Filter by status/date; list cards showing: Cheque No, Amount, Status, Last Update, Bounced Reason (if any)


Payments › Billers


Manage billers list; add new biller (category, consumer no)
Pay Bill form: Select Biller, From Account, Amount, Pay Now / Schedule (date), Reference


Transfers


Tabs: To Own Accounts | To Other iNB Account
Form: From Account, To Account (or select Payee), Amount, Narration, Limits info


Admin › Registrations


Table: Applicant, Type, KYC info (masked), Status, Actions (Approve/Reject)
Drawer for full details; Approve → create account; trigger letter dispatch workflow


Admin › Parameters


Editable fields with effective-from dates:

SavingsInterestRate (% p.a.)
OverdraftAnnualRate (% p.a.)
DailyWithdrawalLimit (₹)
MinSavingsBalance (₹)
ChequeBounceFine (₹)


Audit trail visible on change.

Design Guidance

Minimal color palette; high contrast; focused forms; progressive disclosure.
Mobile responsive (cards → stack; tables → collapsible rows).
Loading states, skeletons, and empty states designed.


12) Accessibility

Semantic HTML, ARIA roles for nav/landmarks.
Keyboard navigable forms and tables; visible focus states.
Color contrast ≥ 4.5:1; avoid color-only cues; error + helper text.
Alt text on icons/images; live regions for async updates.


13) Security Controls

Password hashing with a strong algorithm (e.g., PBKDF2/Argon2).
CSRF tokens on all POST/PUT/DELETE forms.
Anti-XSS encoding; input sanitization server-side.
Strict session management: HttpOnly, Secure, SameSite, short TTL + rolling.
Account lockout after 3 invalid attempts; manual unlock by Admin.
Full audit logging for security-relevant actions.
Least-privilege RBAC across Customer/Banker/Admin.


14) Batch & Scheduling

Daily EOD jobs:

OD interest computation & posting
Savings daily accrual (if daily) or monthly posting if monthly aggregation
Execute scheduled bill payments (with retries)


Scheduler: Windows Task Scheduler / Hangfire / Quartz.NET within IIS-hosted background service (choose per hosting pattern).


15) ASP → ASP.NET Transition Guidance

Architecture: Move to layered solution:

Presentation: ASP.NET MVC/Razor Pages (or ASP.NET Core MVC)
Domain & Application Services: C# class libraries; transaction scripts/use-case services
Data Access: EF Core or Dapper with stored procedures for critical paths
Background Jobs: Hosted service or external scheduler


State Management: Replace in-page state (ViewState) with server session + short-lived temp data; avoid session-heavy design.
Validation: Use Data Annotations + Fluent Validation for complex rules.
Security: Anti-forgery tokens, ASP.NET Identity (or custom) with lockout controls built-in.
Configuration: appsettings.{Environment}.json for parameters; Admin UI writes to DB-backed parameters with effective dates.
IIS:

App pool: No 32-bit unless needed, AlwaysRunning + preload.
Web.config (if .NET Framework) or hosting bundle (if .NET Core) with stdoutLogEnabled for troubleshooting.
ARR/Web Garden: sticky sessions or distributed cache (SQL Server session state/Redis).


Logging: Serilog/NLog to files + database; enable correlation IDs (middleware).
Migration: Strangle pattern—stand up new endpoints while phasing out old pages; data migration scripts; double-run & reconcile for finance-critical tables before cutover.


16) API Endpoints (if exposing internal APIs)

POST /api/registrations
GET /api/registrations?status=Pending
POST /api/registrations/{id}/approve | /reject
POST /api/auth/login
POST /api/auth/unlock/{userId} (Admin)
GET /api/accounts | GET /api/accounts/{id}/transactions?from=&to=
POST /api/cheques/slips
GET /api/cheques/slips?status=
POST /api/payments (immediate or scheduled)
POST /api/transfers
GET /api/reports/reconciliation?asOf=
GET/PUT /api/admin/parameters

All POSTs require CSRF (web) or auth tokens (API).

17) Acceptance Criteria (Samples)

Lockout: After 3 failed logins in a row, user is locked; attempting login shows lockout message; Admin unlock resets attempts; successful login recorded in audit.
Statement: Mini statement shows last 5 transactions sorted desc by date; detailed exports match on-screen total and row count.
OD Interest: For a negative EoD balance of ₹10,000 and OD rate 12% p.a., next day charge = 10000 * 0.12 / 365 = ₹3.29 (rounded to paise).
Cheque: Changing status to Bounced applies configured fine within the same minute and records reason.
Payment Scheduling: Task executes at scheduled local time; if funds insufficient, marks Failed and sends alert.


18) Test Scenarios (High-level)

Registration approve/reject flows; letter dispatch recorded.
Login lockout + manual unlock; audit entries.
Savings min balance enforcement; daily withdrawal cap.
Current with overdraft: below limit, at limit, over limit.
OD daily interest across month-end and leap year.
Cheque lifecycle including bounced + re-submit.
Reconciliation correctness for random as-of dates.
Payment scheduling across public holidays (optional deferral).
Transfer limits & failure modes; idempotency on retry.
Exports: CSV/PDF formatting, totals, encodings.
Role-based access checks.


19) Logging & Audit

Audit: who, what, when, before/after for: login outcomes, lock/unlock, parameter changes, approvals, cheque status transitions, payments/transfers.
Ops logs: job runs, durations, failures, retries.
PII: masked in logs.


20) Configuration Matrix (Admin UI)

ParameterDefaultNotesSavingsInterestRate 3.50% p.a.
GlobalOverdraftAnnualRate 12.00% p.a.
GlobalMinSavingsBalance ₹10,000
GlobalDailyWithdrawalLimit ₹50,000
Savings onlyChequeBounceFine ₹350
Applied on bounceStatementMaxRange 24 months
DetailedSessionTimeout 15 minutes
Idle timeoutLoginAttemptsLockout 3 Fixed by requirement

21) UI Style Guide (Quick)

Typography: System stack (Segoe UI/Inter); 14–16px base; 1.5 line height.
Colors: Primary #0050B3, Secondary #1890FF, Success #52C41A, Warning #FAAD14, Error #F5222D.
Buttons: Primary (solid), Secondary (outlined), Destructive (error).
Forms: Top-aligned labels, helper text, inline validation.
Icons: Simple outline set; consistent size 16/20px.


22) Delivery Plan & Backlog (First 3 Sprints)
Sprint 1

Auth + Lockout + Audit
Registration (front/back) + Admin approvals
Account creation + Home overview

Sprint 2

Accounts statements (mini/detailed) + exports
Savings parameters + min balance + daily withdrawal limits
Current account + overdraft setup + OD daily interest job

Sprint 3

Cheque slips + lifecycle + bounce fines
Bill payments (immediate + scheduler)
Transfers (own + iNB)
Reconciliation report

Hardening

Accessibility fixes, performance profiling, security pen test, IIS tuning, monitoring.


23) Open Questions (for confirmation later, not blocking)

Savings interest accrual: daily simple interest with monthly posting, or monthly simple on monthly average balance?
OD interest: confirm rounding (banker’s rounding to 2 decimals) and posting timing (EOD vs next day start).
Cheque clearance SLAs and holiday calendar integration.
Payment gateway/provider for utility bills (internal vs external integration), and status polling.
Letter dispatch: do we integrate with a courier API or record manually only?
Session timeout & re-authentication for high-risk actions (e.g., unlocking, parameter change).


24) What I can do next for you

Create low‑fidelity wireframes (PPT/PDF) for each screen and share.
Produce a clickable prototype (Figma) for stakeholder sign-off.
Generate entity-relationship (ER) diagram and API Swagger spec.
Draft IIS deployment checklist and DevOps pipeline YAML for CI/CD.

Would you like me to start with wireframes (PPT) and a Swagger YAML for the APIs, or prioritize the ER diagram first?
