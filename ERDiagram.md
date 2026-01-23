# Indian Net Bank (iNB) - ER Diagram

## Entity-Relationship Diagram (Text Representation)

```
+----------------+       +----------------+
|   Customer     |       |   Account      |
+----------------+       +----------------+
| CustomerID (PK)|       | AccountID (PK) |
| FirstName      |       | CustomerID (FK)|
| LastName       |       | AccountType    |
| Username       |       | Balance        |
| Password       |       | InterestRate   |
| Address        |       | MinBalance     |
| City           |       | OverdraftLimit |
| State          |       | CreatedDate    |
| Zip            |       +----------------+
| Phone          |              |
| Email          |              | 1
+----------------+              |
         | 1                    |
         |                      |
         v                      v
+----------------+       +----------------+
|   Transaction  |       |   Cheque       |
+----------------+       +----------------+
| TransactionID  |       | ChequeID (PK)  |
| AccountID (FK) |       | CustomerID (FK)|
| Type           |       | AccountID (FK) |
| Amount         |       | Amount         |
| Date           |       | Status         |
| Description    |       | DepositDate    |
+----------------+       | ClearanceDate  |
                         +----------------+
                                |
                                | 1
                                v
+----------------+       +----------------+
| Bill Payment   |       | Money Transfer |
+----------------+       +----------------+
| PaymentID (PK) |       | TransferID (PK)|
| CustomerID (FK)|       | FromAccount (FK)|
| BillType       |       | ToAccount (FK) |
| Amount         |       | Amount         |
| DueDate        |       | Date           |
| Status         |       +----------------+
+----------------+
```

## Entities and Relationships

### Entities:
- **Customer**: Stores customer personal information.
- **Account**: Represents savings or current accounts, linked to customer.
- **Transaction**: Records all account transactions (deposits, withdrawals, etc.).
- **Cheque**: Details of cheque deposits, linked to customer and account.
- **Bill Payment**: Scheduled or immediate bill payments.
- **Money Transfer**: Transfers between iNB accounts.

### Relationships:
- Customer **1:N** Account (One customer can have multiple accounts)
- Account **1:N** Transaction (One account has many transactions)
- Customer **1:N** Cheque (One customer can deposit multiple cheques)
- Account **1:N** Cheque (Cheques deposited to specific account)
- Customer **1:N** Bill Payment
- Account **1:N** Money Transfer (Transfers from account)

## Database Schema Diagram (Simplified)

```
Database: INB_DB
Tables:
- Customers (CustomerID PK, ...)
- Accounts (AccountID PK, CustomerID FK, ...)
- Transactions (TransactionID PK, AccountID FK, ...)
- Cheques (ChequeID PK, CustomerID FK, AccountID FK, ...)
- BillPayments (PaymentID PK, CustomerID FK, ...)
- MoneyTransfers (TransferID PK, FromAccount FK, ToAccount FK, ...)
```