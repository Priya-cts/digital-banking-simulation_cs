iNB Online Banking – Data Dictionary, Indexes & Retention/Partitioning

Full data dictionary coverage for entities in the conceptual and logical diagrams, with recommended indexes and partitioning/retention strategy for high-volume tables (e.g., TRANSACTION and AUDIT\_EVENT).

Date: 24-Jan-2026

# 1\. Design Drivers (from artifacts)

*   Primary system-of-record is a relational database with strong consistency for customers, accounts, transactions, configuration and reconciliation.
*   MFA mandatory for all users; step-up MFA for high-risk transfers, beneficiary changes, and admin configuration changes.
*   Auditability by design: immutable audit trail for login attempts, MFA challenges, session events, money-moving actions and configuration changes.
*   Data protection: TLS 1.3 in transit and AES-256 at rest with keys managed via KMS/HSM.
*   Retention: application logs >= 90 days, transactional data 3 years, unstructured artifacts >= 10 years with legal hold support.
*   Availability and DR objectives: 99.95% availability; RTO/RPO defined (note: some documents mention 30 vs 60 minutes for RTO).

# 2\. Data Dictionary (All Diagram Entities)

Naming convention: tables in UPPER\_SNAKE\_CASE; PKs as UUID unless constrained otherwise. PII fields should be encrypted/tokenized and masked in logs and non-production environments.

## CUSTOMER

Customer profile and onboarding state (registration/approval).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| customer_id | UUID | PK | Internal identifier. |
| full_name | VARCHAR(200) | NOT NULL | Customer legal name. |
| dob | DATE | NULL | Date of birth (optional). |
| pan_enc | VARBINARY | UNIQUE, NOT NULL | Encrypted/tokenized PAN. |
| aadhaar_hash | VARBINARY(64) | UNIQUE, NOT NULL | Hash of Aadhaar identifier (avoid raw storage). |
| phone | VARCHAR(20) | NOT NULL | Registered phone for MFA and alerts. |
| email | VARCHAR(254) | NOT NULL | Registered email. |
| preferred_language | VARCHAR(10) | DEFAULT "en" | Language preference. |
| onboarding_status | VARCHAR(30) | NOT NULL | PENDING_APPROVAL/APPROVED/REJECTED/SUSPENDED. |
| approved_by | UUID | FK -> USER.user_id, NULL | Approver (staff/admin). |
| approved_at | TIMESTAMP | NULL | Approval timestamp. |
| created_at | TIMESTAMP | NOT NULL | Created timestamp. |
| updated_at | TIMESTAMP | NOT NULL | Updated timestamp. |

*   Recommended Indexes:
*   UQ\_CUSTOMER\_PAN (pan\_enc)
*   UQ\_CUSTOMER\_AADHAAR (aadhaar\_hash)
*   IX\_CUSTOMER\_STATUS (onboarding\_status, created\_at)
*   IX\_CUSTOMER\_PHONE (phone)

## USER

Digital identity mapped to the bank Identity Provider (SAML subject).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| user_id | UUID | PK | Internal user identifier. |
| customer_id | UUID | FK -> CUSTOMER.customer_id, NULL | Link for customer users; NULL for staff/admin/auditor. |
| idp_subject | VARCHAR(200) | UNIQUE, NOT NULL | SAML NameID/subject. |
| username | VARCHAR(100) | UNIQUE, NOT NULL | Login identifier. |
| user_type | VARCHAR(20) | NOT NULL | CUSTOMER/STAFF/ADMIN/AUDITOR. |
| status | VARCHAR(20) | NOT NULL | ACTIVE/LOCKED/DISABLED. |
| locked_until | TIMESTAMP | NULL | Lock expiry (if temporary lockout). |
| last_login_at | TIMESTAMP | NULL | Last successful login. |
| created_at | TIMESTAMP | NOT NULL | Created timestamp. |
| updated_at | TIMESTAMP | NOT NULL | Updated timestamp. |

*   Recommended Indexes:
*   UQ\_USER\_USERNAME (username)
*   UQ\_USER\_IDP\_SUBJECT (idp\_subject)
*   IX\_USER\_TYPE\_STATUS (user\_type, status)
*   IX\_USER\_CUSTOMER (customer\_id)

## ROLE

RBAC role master.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| role_id | UUID | PK | Role identifier. |
| role_name | VARCHAR(50) | UNIQUE, NOT NULL | Role name. |
| description | VARCHAR(250) | NULL | Description. |

*   Recommended Indexes:
*   UQ\_ROLE\_NAME (role\_name)

## USER\_ROLE

User-to-role assignment (many-to-many).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| user_id | UUID | PK, FK -> USER.user_id | User id. |
| role_id | UUID | PK, FK -> ROLE.role_id | Role id. |
| assigned_at | TIMESTAMP | NOT NULL | Assignment time. |
| assigned_by | UUID | FK -> USER.user_id | Admin assigning role. |

*   Recommended Indexes:
*   IX\_USER\_ROLE\_ROLE (role\_id)

## SESSION

Session lifecycle (do not store raw tokens).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| session_id | UUID | PK | Session id. |
| user_id | UUID | FK -> USER.user_id | User. |
| created_at | TIMESTAMP | NOT NULL | Created time. |
| expires_at | TIMESTAMP | NOT NULL | Hard expiry. |
| last_seen_at | TIMESTAMP | NOT NULL | Last activity. |
| ip_address | VARCHAR(45) | NOT NULL | IP address. |
| device_fingerprint | VARCHAR(200) | NULL | Optional device identifier. |
| status | VARCHAR(20) | NOT NULL | ACTIVE/REVOKED/EXPIRED. |
| revoked_reason | VARCHAR(120) | NULL | Reason if revoked. |
| correlation_id | VARCHAR(64) | NULL | Trace id. |

*   Recommended Indexes:
*   IX\_SESSION\_USER\_STATUS (user\_id, status, expires\_at)
*   IX\_SESSION\_EXPIRES (expires\_at)
*   IX\_SESSION\_CORR (correlation\_id)
*   Retention & Archival:
*   Short-lived operational data; retain 30–90 days for investigations, then purge/anonymize (per policy).

## LOGIN\_ATTEMPT

Login attempt tracking for brute-force protection and investigations.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| attempt_id | UUID | PK | Attempt id. |
| user_id | UUID | FK -> USER.user_id, NULL | Known user id if username resolved; NULL for unknown usernames. |
| username | VARCHAR(100) | NULL | Entered username (store hashed if needed to prevent enumeration). |
| attempt_at | TIMESTAMP | NOT NULL | Attempt time. |
| ip_address | VARCHAR(45) | NOT NULL | IP address. |
| device_fingerprint | VARCHAR(200) | NULL | Device id. |
| outcome | VARCHAR(20) | NOT NULL | SUCCESS/FAILURE. |
| failure_reason | VARCHAR(50) | NULL | INVALID_PASSWORD/MFA_FAILED/LOCKED/etc. |
| correlation_id | VARCHAR(64) | NULL | Trace id. |

*   Recommended Indexes:
*   IX\_LOGIN\_USER\_TIME (user\_id, attempt\_at DESC)
*   IX\_LOGIN\_IP\_TIME (ip\_address, attempt\_at DESC)
*   IX\_LOGIN\_USERNAME\_TIME (username, attempt\_at DESC)
*   IX\_LOGIN\_OUTCOME\_TIME (outcome, attempt\_at DESC)
*   Partitioning Strategy:
*   Partition by RANGE(attempt\_at) monthly if volume is high; maintain last 90 days in hot partitions.
*   Retention & Archival:
*   Keep >= 90 days aligned with application log retention; archive longer only if required for security investigations/legal hold.

## MFA\_METHOD

Registered MFA methods for users (SMS OTP / TOTP).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| method_id | UUID | PK | Method id. |
| user_id | UUID | FK -> USER.user_id | Owner user. |
| method_type | VARCHAR(20) | NOT NULL | SMS/TOTP. |
| masked_destination | VARCHAR(80) | NULL | Masked phone/email. |
| totp_secret_ref | VARCHAR(200) | NULL | Reference to secret in KMS/HSM. |
| enabled | BOOLEAN | NOT NULL | Enabled. |
| verified_at | TIMESTAMP | NULL | Verified time. |
| created_at | TIMESTAMP | NOT NULL | Created time. |
| updated_at | TIMESTAMP | NOT NULL | Updated time. |

*   Recommended Indexes:
*   IX\_MFA\_METHOD\_USER (user\_id, enabled)
*   IX\_MFA\_METHOD\_TYPE (method\_type)

## MFA\_CHALLENGE

OTP/MFA challenge for login or step-up high-risk actions; supports retry limits.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| challenge_id | UUID | PK | Challenge id. |
| user_id | UUID | FK -> USER.user_id | User. |
| method_id | UUID | FK -> MFA_METHOD.method_id | Method. |
| purpose | VARCHAR(30) | NOT NULL | LOGIN/HIGH_RISK_ACTION. |
| related_entity_type | VARCHAR(40) | NULL | Entity type requiring step-up (e.g., TRANSFER_INSTRUCTION). |
| related_entity_id | UUID | NULL | Related entity id. |
| otp_hash | VARBINARY(64) | NULL | Hash for SMS OTP (optional). |
| expires_at | TIMESTAMP | NOT NULL | Expiry. |
| attempt_count | INT | NOT NULL DEFAULT 0 | Failed attempts. |
| locked_until | TIMESTAMP | NULL | Cooldown until (if locked). |
| status | VARCHAR(20) | NOT NULL | PENDING/VERIFIED/LOCKED/EXPIRED. |
| created_at | TIMESTAMP | NOT NULL | Created time. |
| verified_at | TIMESTAMP | NULL | Verified time. |
| correlation_id | VARCHAR(64) | NULL | Trace id. |

*   Recommended Indexes:
*   IX\_MFA\_CHALLENGE\_USER\_STATUS (user\_id, status, expires\_at)
*   IX\_MFA\_CHALLENGE\_RELATED (related\_entity\_type, related\_entity\_id)
*   IX\_MFA\_CHALLENGE\_CORR (correlation\_id)
*   Partitioning Strategy:
*   Partition by RANGE(created\_at) monthly if needed.
*   Retention & Archival:
*   Operational: keep 90 days (security investigations), then purge.

## KYC\_DOCUMENT

KYC artifacts stored in object storage; DB keeps metadata and pointers.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| kyc_doc_id | UUID | PK | KYC document id. |
| customer_id | UUID | FK -> CUSTOMER.customer_id | Customer. |
| doc_type | VARCHAR(30) | NOT NULL | PAN/AADHAAR/PHOTO/ADDRESS_PROOF etc. |
| object_uri | VARCHAR(500) | NOT NULL | Object storage URI. |
| content_hash | VARBINARY(32) | NOT NULL | SHA-256 hash for immutability. |
| mime_type | VARCHAR(80) | NOT NULL | Content type. |
| uploaded_at | TIMESTAMP | NOT NULL | Upload time. |
| uploaded_by | UUID | FK -> USER.user_id, NULL | Uploader (customer or staff). |
| status | VARCHAR(20) | NOT NULL | SUBMITTED/VERIFIED/REJECTED. |
| verified_by | UUID | FK -> USER.user_id, NULL | Verifier. |
| verified_at | TIMESTAMP | NULL | Verification time. |

*   Recommended Indexes:
*   IX\_KYC\_CUSTOMER\_TYPE (customer\_id, doc\_type)
*   IX\_KYC\_STATUS (status, uploaded\_at)
*   Retention & Archival:
*   Object storage retention >= 10 years (minimum) with legal hold; DB metadata aligned with object lifecycle.

## ACCOUNT

Savings/Current account master.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| account_id | UUID | PK | Account id. |
| customer_id | UUID | FK -> CUSTOMER.customer_id | Owner. |
| account_number | VARCHAR(30) | UNIQUE, NOT NULL | Account number. |
| account_type | VARCHAR(20) | NOT NULL | SAVINGS/CURRENT. |
| branch_code | VARCHAR(20) | NOT NULL | Branch. |
| currency | CHAR(3) | NOT NULL | Currency. |
| status | VARCHAR(20) | NOT NULL | ACTIVE/CLOSED/FROZEN. |
| opened_at | DATE | NOT NULL | Open date. |
| closed_at | DATE | NULL | Close date. |
| created_at | TIMESTAMP | NOT NULL | Created time. |
| updated_at | TIMESTAMP | NOT NULL | Updated time. |

*   Recommended Indexes:
*   IX\_ACCOUNT\_CUSTOMER (customer\_id)
*   IX\_ACCOUNT\_BRANCH (branch\_code)
*   IX\_ACCOUNT\_STATUS (status)

## ACCOUNT\_BALANCE

Current balance snapshot per account for fast dashboard queries (authoritative ledger remains TRANSACTION/entries).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| account_id | UUID | PK, FK -> ACCOUNT.account_id | Account id. |
| available_balance | DECIMAL(18,2) | NOT NULL | Available balance. |
| ledger_balance | DECIMAL(18,2) | NOT NULL | Ledger balance. |
| as_of | TIMESTAMP | NOT NULL | Snapshot timestamp. |
| overdraft_used | DECIMAL(18,2) | NOT NULL DEFAULT 0 | Overdraft used (current accounts). |

*   Recommended Indexes:
*   IX\_BALANCE\_ASOF (as\_of)

## ACCOUNT\_LIMIT

Per-customer/per-account configurable limits (withdrawal/transfer/overdraft eligibility).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| limit_id | UUID | PK | Limit id. |
| account_id | UUID | FK -> ACCOUNT.account_id | Account. |
| limit_type | VARCHAR(40) | NOT NULL | DAILY_TRANSFER/MAX_TXN_AMOUNT/OD_LIMIT etc. |
| limit_value | DECIMAL(18,2) | NOT NULL | Value. |
| currency | CHAR(3) | NOT NULL | Currency. |
| effective_from | TIMESTAMP | NOT NULL | Effective start. |
| effective_to | TIMESTAMP | NULL | Effective end. |
| configured_by | UUID | FK -> USER.user_id | Who configured. |
| configured_at | TIMESTAMP | NOT NULL | When configured. |

*   Recommended Indexes:
*   IX\_LIMIT\_ACCOUNT\_TYPE (account\_id, limit\_type, effective\_from DESC)

## IDEMPOTENCY\_KEY

Idempotency keys for money-moving APIs to prevent duplicates during retries/timeouts.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| key_id | UUID | PK | Key id. |
| owner_user_id | UUID | FK -> USER.user_id | Owner. |
| key_hash | VARBINARY(32) | UNIQUE, NOT NULL | Hash of client-provided idempotency key. |
| scope | VARCHAR(30) | NOT NULL | TRANSFER/BILL_PAYMENT/etc. |
| request_fingerprint | VARBINARY(32) | NOT NULL | Hash of canonical request payload. |
| status | VARCHAR(20) | NOT NULL | IN_PROGRESS/COMPLETED/FAILED. |
| response_ref | VARCHAR(200) | NULL | Pointer to stored response summary. |
| created_at | TIMESTAMP | NOT NULL | Created time. |
| expires_at | TIMESTAMP | NOT NULL | Expiry for reuse window. |

*   Recommended Indexes:
*   UQ\_IDEMPOTENCY\_HASH (key\_hash)
*   IX\_IDEMPOTENCY\_OWNER (owner\_user\_id, created\_at DESC)
*   IX\_IDEMPOTENCY\_EXPIRES (expires\_at)
*   Retention & Archival:
*   Keep for at least the maximum client retry window (e.g., 24–72 hours) and for audit correlation (e.g., 90 days) depending on policy.

## TRANSACTION

Posted ledger transaction (statements include posted transactions only).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| txn_id | UUID | PK | Transaction id. |
| account_id | UUID | FK -> ACCOUNT.account_id | Account. |
| txn_type | VARCHAR(30) | NOT NULL | TRANSFER/BILL_PAYMENT/CHEQUE/INTEREST/FEE etc. |
| direction | CHAR(1) | NOT NULL | D=debit, C=credit. |
| amount | DECIMAL(18,2) | NOT NULL | Absolute amount. |
| currency | CHAR(3) | NOT NULL | Currency. |
| posted_at | TIMESTAMP | NOT NULL | Posting time. |
| value_date | DATE | NOT NULL | Value date. |
| status | VARCHAR(20) | NOT NULL | POSTED/REVERSED. |
| reference | VARCHAR(64) | UNIQUE | Customer reference. |
| related_entity_type | VARCHAR(40) | NULL | TRANSFER_INSTRUCTION/BILL_PAYMENT/CHEQUE_DEPOSIT etc. |
| related_entity_id | UUID | NULL | Related entity id. |
| narration | VARCHAR(250) | NULL | Narration/description. |
| correlation_id | VARCHAR(64) | NULL | Trace id. |

*   Recommended Indexes:
*   IX\_TXN\_ACCOUNT\_POSTED (account\_id, posted\_at DESC)
*   IX\_TXN\_ACCOUNT\_VALUE (account\_id, value\_date DESC)
*   IX\_TXN\_TYPE\_TIME (txn\_type, posted\_at DESC)
*   UQ\_TXN\_REFERENCE (reference)
*   IX\_TXN\_RELATED (related\_entity\_type, related\_entity\_id)
*   IX\_TXN\_CORR (correlation\_id)
*   Partitioning Strategy:
*   Partition by RANGE(posted\_at) monthly/quarterly; subpartition by HASH(account\_id) if supported and needed for parallelism.
*   Keep recent partitions (e.g., last 3–6 months) on faster storage; older partitions on cheaper storage.
*   Retention & Archival:
*   Transactional data retained for 3 years minimum; after retention, archive partitions to cold storage or purge per compliance policy.
*   Support legal hold: prevent deletion of partitions under hold; export immutable copies if required.

## BENEFICIARY

Saved beneficiaries for transfers.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| beneficiary_id | UUID | PK | Beneficiary id. |
| customer_id | UUID | FK -> CUSTOMER.customer_id | Owner customer. |
| name | VARCHAR(200) | NOT NULL | Beneficiary name. |
| bank_name | VARCHAR(200) | NULL | Bank name. |
| ifsc | VARCHAR(16) | NULL | IFSC. |
| account_number | VARCHAR(30) | NOT NULL | Beneficiary account. |
| beneficiary_type | VARCHAR(20) | NOT NULL | INTERNAL/EXTERNAL. |
| status | VARCHAR(20) | NOT NULL | ACTIVE/DISABLED/PENDING_VERIFICATION. |
| created_at | TIMESTAMP | NOT NULL | Created time. |
| updated_at | TIMESTAMP | NOT NULL | Updated time. |

*   Recommended Indexes:
*   IX\_BENE\_CUSTOMER (customer\_id, status)
*   IX\_BENE\_ACCT (account\_number)

## TRANSFER\_INSTRUCTION

Transfer request/instruction (internal/NEFT/RTGS).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| transfer_id | UUID | PK | Transfer id. |
| customer_id | UUID | FK -> CUSTOMER.customer_id | Initiator. |
| source_account_id | UUID | FK -> ACCOUNT.account_id | Debit account. |
| beneficiary_id | UUID | FK -> BENEFICIARY.beneficiary_id | Beneficiary. |
| amount | DECIMAL(18,2) | NOT NULL | Amount. |
| currency | CHAR(3) | NOT NULL | Currency. |
| mode | VARCHAR(20) | NOT NULL | INTERNAL/NEFT/RTGS. |
| status | VARCHAR(20) | NOT NULL | INITIATED/PROCESSING/COMPLETED/FAILED. |
| idempotency_key_id | UUID | FK -> IDEMPOTENCY_KEY.key_id | Idempotency. |
| risk_score | DECIMAL(5,2) | NULL | Risk score (if available). |
| created_at | TIMESTAMP | NOT NULL | Created. |
| completed_at | TIMESTAMP | NULL | Completed. |
| reference | VARCHAR(64) | UNIQUE | Customer reference. |
| correlation_id | VARCHAR(64) | NULL | Trace id. |

*   Recommended Indexes:
*   IX\_TRF\_SRC\_TIME (source\_account\_id, created\_at DESC)
*   IX\_TRF\_CUST\_TIME (customer\_id, created\_at DESC)
*   IX\_TRF\_STATUS\_TIME (status, created\_at DESC)
*   UQ\_TRF\_REFERENCE (reference)

## TRANSFER\_EXECUTION

Execution attempts and external network references for a transfer.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| execution_id | UUID | PK | Execution id. |
| transfer_id | UUID | FK -> TRANSFER_INSTRUCTION.transfer_id | Parent transfer. |
| attempt_no | INT | NOT NULL | Attempt number. |
| network | VARCHAR(20) | NOT NULL | INTERNAL/NEFT/RTGS. |
| external_reference | VARCHAR(120) | NULL | NEFT/RTGS reference. |
| status | VARCHAR(20) | NOT NULL | SENT/ACKED/SETTLED/FAILED. |
| sent_at | TIMESTAMP | NULL | Sent time. |
| updated_at | TIMESTAMP | NOT NULL | Last update. |
| failure_code | VARCHAR(40) | NULL | Failure code. |

*   Recommended Indexes:
*   IX\_TRF\_EXEC\_TRF (transfer\_id, attempt\_no)
*   IX\_TRF\_EXEC\_STATUS (status, updated\_at DESC)

## BILLER

Biller master list integrated via bank biller APIs.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| biller_id | UUID | PK | Biller id. |
| name | VARCHAR(200) | NOT NULL | Name. |
| category | VARCHAR(50) | NULL | Category. |
| api_reference | VARCHAR(100) | UNIQUE, NOT NULL | External identifier. |
| active | BOOLEAN | NOT NULL | Active flag. |
| created_at | TIMESTAMP | NOT NULL | Created time. |

*   Recommended Indexes:
*   UQ\_BILLER\_APIREF (api\_reference)
*   IX\_BILLER\_ACTIVE (active, name)

## BILL\_ACCOUNT

Customer-specific biller account (consumer number) used for payments and recurring schedules.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| bill_account_id | UUID | PK | Bill account id. |
| customer_id | UUID | FK -> CUSTOMER.customer_id | Customer. |
| biller_id | UUID | FK -> BILLER.biller_id | Biller. |
| consumer_number | VARCHAR(60) | NOT NULL | Consumer/account number at biller. |
| nickname | VARCHAR(80) | NULL | Friendly name. |
| status | VARCHAR(20) | NOT NULL | ACTIVE/DISABLED. |
| created_at | TIMESTAMP | NOT NULL | Created. |
| updated_at | TIMESTAMP | NOT NULL | Updated. |

*   Recommended Indexes:
*   IX\_BILLACC\_CUST (customer\_id, status)
*   IX\_BILLACC\_BILLER (biller\_id)
*   UQ\_BILLACC (biller\_id, consumer\_number, customer\_id)

## PAYMENT\_SCHEDULE

Recurring payment schedule (monthly) for a bill account.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| schedule_id | UUID | PK | Schedule id. |
| bill_account_id | UUID | FK -> BILL_ACCOUNT.bill_account_id | Bill account. |
| frequency | VARCHAR(20) | NOT NULL | MONTHLY (initial scope). |
| day_of_month | INT | NOT NULL | 1-28/30/31 depending rules. |
| start_date | DATE | NOT NULL | Start date. |
| end_date | DATE | NULL | End date. |
| max_amount | DECIMAL(18,2) | NULL | Optional cap. |
| status | VARCHAR(20) | NOT NULL | ACTIVE/PAUSED/CANCELLED. |
| created_at | TIMESTAMP | NOT NULL | Created. |
| updated_at | TIMESTAMP | NOT NULL | Updated. |

*   Recommended Indexes:
*   IX\_SCHED\_BILLACC (bill\_account\_id, status)
*   IX\_SCHED\_RUN (status, day\_of\_month)

## BILL\_PAYMENT

Bill payment execution via payment gateway with real-time confirmation.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| bill_payment_id | UUID | PK | Bill payment id. |
| customer_id | UUID | FK -> CUSTOMER.customer_id | Customer. |
| source_account_id | UUID | FK -> ACCOUNT.account_id | Debit account. |
| bill_account_id | UUID | FK -> BILL_ACCOUNT.bill_account_id | Bill account. |
| amount | DECIMAL(18,2) | NOT NULL | Amount. |
| currency | CHAR(3) | NOT NULL | Currency. |
| is_recurring | BOOLEAN | NOT NULL | Recurring flag. |
| schedule_id | UUID | FK -> PAYMENT_SCHEDULE.schedule_id, NULL | Schedule when recurring. |
| gateway | VARCHAR(30) | NOT NULL | PAYPAL (initial). |
| gateway_reference | VARCHAR(100) | NULL | Gateway reference. |
| status | VARCHAR(20) | NOT NULL | INITIATED/COMPLETED/FAILED. |
| idempotency_key_id | UUID | FK -> IDEMPOTENCY_KEY.key_id | Idempotency. |
| created_at | TIMESTAMP | NOT NULL | Created. |
| completed_at | TIMESTAMP | NULL | Completed. |
| reference | VARCHAR(64) | UNIQUE | Customer reference. |

*   Recommended Indexes:
*   IX\_BPAY\_SRC\_TIME (source\_account\_id, created\_at DESC)
*   IX\_BPAY\_STATUS\_TIME (status, created\_at DESC)
*   UQ\_BPAY\_REF (reference)

## CHEQUE\_DEPOSIT

Cheque deposit request and lifecycle.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| cheque_deposit_id | UUID | PK | Cheque deposit id. |
| customer_id | UUID | FK -> CUSTOMER.customer_id | Customer. |
| account_id | UUID | FK -> ACCOUNT.account_id | Credit account. |
| cheque_number | VARCHAR(30) | NOT NULL | Cheque no. |
| drawer_bank | VARCHAR(200) | NULL | Drawer bank. |
| amount | DECIMAL(18,2) | NOT NULL | Amount. |
| currency | CHAR(3) | NOT NULL | Currency. |
| submitted_at | TIMESTAMP | NOT NULL | Submitted. |
| current_status | VARCHAR(30) | NOT NULL | RECEIVED/IN_CLEARANCE/CLEARED/BOUNCED. |
| clearance_sla_days | INT | NOT NULL | Configurable SLA days. |
| bounce_penalty_amount | DECIMAL(18,2) | NULL | Penalty. |
| updated_at | TIMESTAMP | NOT NULL | Updated. |

*   Recommended Indexes:
*   IX\_CHQ\_ACC\_TIME (account\_id, submitted\_at DESC)
*   IX\_CHQ\_STATUS\_TIME (current\_status, submitted\_at DESC)

## CHEQUE\_STATUS\_HISTORY

Status change history for cheque deposits (for audit and notifications).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| history_id | UUID | PK | History id. |
| cheque_deposit_id | UUID | FK -> CHEQUE_DEPOSIT.cheque_deposit_id | Cheque deposit. |
| old_status | VARCHAR(30) | NOT NULL | Old status. |
| new_status | VARCHAR(30) | NOT NULL | New status. |
| changed_by | UUID | FK -> USER.user_id | Staff/admin who changed status. |
| changed_at | TIMESTAMP | NOT NULL | Change time. |
| remarks | VARCHAR(250) | NULL | Remarks. |

*   Recommended Indexes:
*   IX\_CHQ\_HIST\_DEP\_TIME (cheque\_deposit\_id, changed\_at DESC)

## STATEMENT\_REQUEST

Statement generation requests (mini/detailed), including export format.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| statement_req_id | UUID | PK | Request id. |
| account_id | UUID | FK -> ACCOUNT.account_id | Account. |
| request_type | VARCHAR(20) | NOT NULL | MINI/DETAILED. |
| from_date | DATE | NULL | Start date for detailed. |
| to_date | DATE | NULL | End date for detailed. |
| format | VARCHAR(10) | NOT NULL | PDF/EXCEL. |
| status | VARCHAR(20) | NOT NULL | REQUESTED/GENERATING/READY/FAILED. |
| created_by | UUID | FK -> USER.user_id | Requester. |
| created_at | TIMESTAMP | NOT NULL | Created. |
| completed_at | TIMESTAMP | NULL | Completed. |
| correlation_id | VARCHAR(64) | NULL | Trace id. |

*   Recommended Indexes:
*   IX\_STMT\_ACC\_TIME (account\_id, created\_at DESC)
*   IX\_STMT\_STATUS\_TIME (status, created\_at DESC)

## STATEMENT\_ARTIFACT

Generated statement file stored in object storage.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| artifact_id | UUID | PK | Artifact id. |
| statement_req_id | UUID | FK -> STATEMENT_REQUEST.statement_req_id | Request. |
| object_uri | VARCHAR(500) | NOT NULL | Object URI. |
| content_hash | VARBINARY(32) | NOT NULL | Hash for immutability. |
| size_bytes | BIGINT | NOT NULL | Size. |
| generated_at | TIMESTAMP | NOT NULL | Generated time. |
| expires_at | TIMESTAMP | NULL | Optional short-lived link expiry. |
| mime_type | VARCHAR(80) | NOT NULL | Content type. |

*   Recommended Indexes:
*   IX\_STMT\_ART\_REQ (statement\_req\_id)
*   IX\_STMT\_ART\_GEN (generated\_at DESC)
*   Retention & Archival:
*   Statement exports stored as unstructured artifacts; retain per unstructured retention (>=10 years) or per bank policy; consider shorter retention for exports if re-generatable.

## RECONCILIATION\_RUN

Daily/monthly reconciliation batch run metadata.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| recon_run_id | UUID | PK | Run id. |
| run_type | VARCHAR(20) | NOT NULL | DAILY/MONTHLY. |
| business_date | DATE | NOT NULL | Date reconciled. |
| status | VARCHAR(20) | NOT NULL | STARTED/COMPLETED/FAILED. |
| started_at | TIMESTAMP | NOT NULL | Start. |
| completed_at | TIMESTAMP | NULL | Complete. |
| generated_by | UUID | FK -> USER.user_id | Staff/auditor initiating. |
| report_object_uri | VARCHAR(500) | NULL | Exported report object. |
| correlation_id | VARCHAR(64) | NULL | Trace id. |

*   Recommended Indexes:
*   IX\_RECON\_DATE (business\_date, run\_type)
*   IX\_RECON\_STATUS (status, started\_at DESC)

## RECONCILIATION\_ITEM

Per-transaction reconciliation result items.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| recon_item_id | UUID | PK | Item id. |
| recon_run_id | UUID | FK -> RECONCILIATION_RUN.recon_run_id | Run. |
| txn_id | UUID | FK -> TRANSACTION.txn_id | Transaction. |
| match_status | VARCHAR(20) | NOT NULL | MATCHED/UNMATCHED/EXCEPTION. |
| notes | VARCHAR(250) | NULL | Notes. |
| created_at | TIMESTAMP | NOT NULL | Created. |

*   Recommended Indexes:
*   IX\_RECON\_ITEM\_RUN (recon\_run\_id, match\_status)
*   IX\_RECON\_ITEM\_TXN (txn\_id)

## NOTIFICATION

Outbound notifications (SMS/email) with delivery status.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| notification_id | UUID | PK | Notification id. |
| recipient_user_id | UUID | FK -> USER.user_id, NULL | Recipient (if known). |
| channel | VARCHAR(10) | NOT NULL | SMS/EMAIL. |
| template_code | VARCHAR(50) | NOT NULL | Template identifier. |
| destination | VARCHAR(254) | NOT NULL | Phone/email (masked in logs). |
| payload_ref | VARCHAR(200) | NULL | Reference to payload stored securely. |
| status | VARCHAR(20) | NOT NULL | QUEUED/SENT/DELIVERED/FAILED. |
| provider_message_id | VARCHAR(120) | NULL | Provider id. |
| related_entity_type | VARCHAR(40) | NULL | Related entity type. |
| related_entity_id | UUID | NULL | Related entity id. |
| created_at | TIMESTAMP | NOT NULL | Created. |
| sent_at | TIMESTAMP | NULL | Sent. |
| delivered_at | TIMESTAMP | NULL | Delivered. |

*   Recommended Indexes:
*   IX\_NOTIF\_RECIP\_TIME (recipient\_user\_id, created\_at DESC)
*   IX\_NOTIF\_STATUS\_TIME (status, created\_at DESC)
*   IX\_NOTIF\_RELATED (related\_entity\_type, related\_entity\_id)
*   Retention & Archival:
*   Operational; keep 90–180 days for support, then purge payload; retain only minimal metadata if required.

## FRAUD\_ALERT

Fraud/risk flags for transactions/transfers/bill payments requiring staff review.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| fraud_alert_id | UUID | PK | Alert id. |
| entity_type | VARCHAR(40) | NOT NULL | TRANSFER_INSTRUCTION/BILL_PAYMENT/TRANSACTION. |
| entity_id | UUID | NOT NULL | Entity id. |
| risk_score | DECIMAL(5,2) | NOT NULL | Risk score. |
| rule_code | VARCHAR(50) | NOT NULL | Triggered rule. |
| status | VARCHAR(20) | NOT NULL | OPEN/UNDER_REVIEW/CLOSED/ESCALATED. |
| created_at | TIMESTAMP | NOT NULL | Created. |
| reviewed_by | UUID | FK -> USER.user_id, NULL | Reviewer. |
| reviewed_at | TIMESTAMP | NULL | Reviewed. |
| outcome | VARCHAR(30) | NULL | APPROVED/REJECTED/CONFIRMED_FRAUD etc. |
| notes | VARCHAR(500) | NULL | Investigation notes. |

*   Recommended Indexes:
*   IX\_FRAUD\_ENTITY (entity\_type, entity\_id)
*   IX\_FRAUD\_STATUS\_TIME (status, created\_at DESC)
*   IX\_FRAUD\_SCORE (risk\_score DESC)
*   Partitioning Strategy:
*   Partition by RANGE(created\_at) monthly if alert volume is high.
*   Retention & Archival:
*   Retain per fraud/compliance policy; align with transaction retention (>=3 years) if used for dispute investigations.

## AUDIT\_EVENT

Immutable audit trail for security-sensitive and business-critical actions.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| audit_id | UUID | PK | Audit id. |
| actor_user_id | UUID | FK -> USER.user_id | Actor. |
| actor_role | VARCHAR(50) | NOT NULL | Role at time. |
| action | VARCHAR(80) | NOT NULL | Action code. |
| entity_type | VARCHAR(60) | NOT NULL | Target entity type. |
| entity_id | UUID | NOT NULL | Target entity id. |
| outcome | VARCHAR(20) | NOT NULL | SUCCESS/FAILURE. |
| timestamp | TIMESTAMP | NOT NULL | Event time. |
| ip_address | VARCHAR(45) | NOT NULL | IP. |
| device_metadata | VARCHAR(400) | NULL | Device/user-agent. |
| correlation_id | VARCHAR(64) | NULL | Correlation id. |
| details_json | JSON | NULL | Additional fields (masked). |

*   Recommended Indexes:
*   IX\_AUDIT\_TIME (timestamp DESC)
*   IX\_AUDIT\_ACTOR\_TIME (actor\_user\_id, timestamp DESC)
*   IX\_AUDIT\_ENTITY (entity\_type, entity\_id, timestamp DESC)
*   IX\_AUDIT\_ACTION\_TIME (action, timestamp DESC)
*   IX\_AUDIT\_OUTCOME\_TIME (outcome, timestamp DESC)
*   IX\_AUDIT\_CORR (correlation\_id)
*   Partitioning Strategy:
*   Partition by RANGE(timestamp) monthly/quarterly; keep recent partitions hot for investigations/reporting.
*   Consider separate storage/tablespace for audit partitions to enforce append-only and access control (WORM where feasible).
*   Retention & Archival:
*   Retain per compliance needs; at minimum align with legal/audit requirements. Store immutable copies in SIEM/object storage if required.
*   Support legal hold and eDiscovery: prevent deletion of partitions under hold.

## CONFIG\_ITEM

Versioned configuration items (limits, rates, SLAs, penalties).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| config_id | UUID | PK | Config id. |
| config_key | VARCHAR(100) | NOT NULL | Key name. |
| scope_type | VARCHAR(20) | NOT NULL | GLOBAL/BRANCH/CUSTOMER. |
| scope_id | VARCHAR(60) | NULL | Branch code or customer id. |
| value_json | JSON | NOT NULL | Value. |
| version | INT | NOT NULL | Version. |
| status | VARCHAR(20) | NOT NULL | DRAFT/ACTIVE/RETIRED. |
| effective_from | TIMESTAMP | NOT NULL | Start. |
| effective_to | TIMESTAMP | NULL | End. |
| created_by | UUID | FK -> USER.user_id | Maker. |
| created_at | TIMESTAMP | NOT NULL | Created. |

*   Recommended Indexes:
*   IX\_CONFIG\_KEY\_SCOPE (config\_key, scope\_type, scope\_id, status)
*   IX\_CONFIG\_EFFECTIVE (effective\_from DESC)

## CONFIG\_APPROVAL

Maker-checker approval workflow for high-impact configuration changes.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| approval_id | UUID | PK | Approval id. |
| config_id | UUID | FK -> CONFIG_ITEM.config_id | Config item. |
| requested_by | UUID | FK -> USER.user_id | Maker. |
| requested_at | TIMESTAMP | NOT NULL | Requested time. |
| approved_by | UUID | FK -> USER.user_id, NULL | Checker. |
| approved_at | TIMESTAMP | NULL | Approval time. |
| status | VARCHAR(20) | NOT NULL | PENDING/APPROVED/REJECTED. |
| comment | VARCHAR(250) | NULL | Comment. |

*   Recommended Indexes:
*   IX\_CFG\_APPR\_STATUS (status, requested\_at DESC)
*   IX\_CFG\_APPR\_CONFIG (config\_id)

# 3\. Indexing Guidelines (Cross-cutting)

Guidance applies regardless of RDBMS; adjust syntax to platform (SQL Server/Oracle/PostgreSQL).

*   Prefer composite indexes that match the most common query patterns (e.g., account\_id + posted\_at for statements and dashboards).
*   Use descending indexes on time columns for “latest N” queries (e.g., last 5 transactions).
*   For lookups by external reference (gateway/NEFT/RTGS), ensure unique or selective indexes.
*   Keep PII out of indexes where possible; index hashed/tokenized forms instead of raw identifiers.
*   Monitor index bloat and write amplification for high-volume tables (TRANSACTION, AUDIT\_EVENT); avoid over-indexing.

# 4\. Partitioning & Retention Strategy (High-volume Tables)

Strategy assumes 10k transactions/day initially with growth; partitions enable predictable retention and faster range queries.

## 4.1 TRANSACTION

*   Partition key: posted\_at (RANGE). Create monthly partitions (or quarterly if volume remains low).
*   Primary access patterns: statements by account/date range, dashboards by account/latest N. Use IX\_TXN\_ACCOUNT\_POSTED (account\_id, posted\_at DESC).
*   Archival: detach/archive partitions older than 3 years to cold storage; optionally keep summarized aggregates for analytics.
*   Legal hold: mark partitions under hold as non-droppable; export immutable snapshot if required.

## 4.2 AUDIT\_EVENT

*   Partition key: timestamp (RANGE). Monthly partitions; use separate tablespace/filegroup if available.
*   Immutability: append-only permissions; consider WORM storage exports to SIEM/object storage for tamper resistance.
*   Query patterns: by actor/time, by entity/time, and by correlation\_id for incident investigations. Index accordingly.
*   Retention: align with compliance/audit needs; where uncertain, retain at least as long as transactional retention and legal hold requirements.

## 4.3 LOGIN\_ATTEMPT / MFA\_CHALLENGE / NOTIFICATION

*   Operational/security telemetry tables: partition monthly by time columns and keep a rolling window (>= 90 days) in hot storage.
*   Purge payload-heavy columns first (e.g., notification payloads) while keeping minimal metadata for troubleshooting.
*   Archive only when mandated by investigations/legal hold.

## 4.4 Unstructured Artifacts (KYC\_DOCUMENT, STATEMENT\_ARTIFACT, Reports)

*   Store in FRRS-compliant object storage with encryption and access logging; DB keeps immutable pointers and hashes.
*   Retention: minimum 10 years for KYC/unstructured artifacts; reconcile with bank policy on statement exports (may be re-generatable).
*   Implement lifecycle policies (tiering, archival) and legal hold flags.