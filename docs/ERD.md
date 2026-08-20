# ERD — PT. XYZ Online Loan

## 1. Entity Relationship

```text
USER
 |
 | 1
 |
 | N
LOAN
 |
 | 1
 |
 | N
INSTALLMENT
 |
 | 1
 |
 | N
PAYMENT

USER
 |
 | 1
 |
 | N
NOTIFICATION

USER
 |
 | 1
 |
 | N
DOCUMENT
```

## 2. USER

| Column | Type | Key | Description |
|---|---|---|---|
| id | UUID | PK | User identifier |
| full_name | VARCHAR(100) | | User name |
| email | VARCHAR(150) | UNIQUE | Email |
| phone | VARCHAR(20) | UNIQUE | Phone number |
| password_hash | VARCHAR(255) | | Hashed password |
| biometric_enabled | BOOLEAN | | Biometric preference |
| status | VARCHAR(30) | | Account status |
| created_at | TIMESTAMP | | Creation time |
| updated_at | TIMESTAMP | | Last update |

## 3. DOCUMENT

| Column | Type | Key | Description |
|---|---|---|---|
| id | UUID | PK | Document ID |
| user_id | UUID | FK | User |
| type | VARCHAR(30) | | KTP/SELFIE |
| file_url | TEXT | | Private object storage reference |
| verification_status | VARCHAR(30) | | Pending/verified/rejected |
| created_at | TIMESTAMP | | Upload time |

## 4. LOAN

| Column | Type | Key | Description |
|---|---|---|---|
| id | UUID | PK | Loan ID |
| user_id | UUID | FK | Borrower |
| amount | BIGINT | | Principal amount |
| tenor_months | INT | | Tenor |
| interest_rate | DECIMAL | | Interest rate |
| monthly_installment | BIGINT | | Monthly installment |
| remaining_debt | BIGINT | | Outstanding debt |
| status | VARCHAR(30) | | Loan status |
| rejection_reason | TEXT | | Rejection reason |
| submitted_at | TIMESTAMP | | Application time |
| approved_at | TIMESTAMP | | Approval time |
| created_at | TIMESTAMP | | Creation time |

## 5. INSTALLMENT

| Column | Type | Key | Description |
|---|---|---|---|
| id | UUID | PK | Installment ID |
| loan_id | UUID | FK | Loan |
| installment_number | INT | | Sequence |
| due_date | DATE | | Due date |
| amount | BIGINT | | Installment amount |
| paid_amount | BIGINT | | Paid amount |
| status | VARCHAR(30) | | Payment status |
| paid_at | TIMESTAMP | | Payment time |

## 6. PAYMENT

| Column | Type | Key | Description |
|---|---|---|---|
| id | UUID | PK | Payment ID |
| loan_id | UUID | FK | Loan |
| installment_id | UUID | FK | Installment |
| amount | BIGINT | | Payment amount |
| payment_method | VARCHAR(50) | | Payment method |
| status | VARCHAR(30) | | Payment status |
| paid_at | TIMESTAMP | | Payment timestamp |

## 7. NOTIFICATION

| Column | Type | Key | Description |
|---|---|---|---|
| id | UUID | PK | Notification ID |
| user_id | UUID | FK | Recipient |
| type | VARCHAR(50) | | Notification type |
| title | VARCHAR(150) | | Title |
| message | TEXT | | Message |
| channel | VARCHAR(20) | | Push/email/SMS |
| is_read | BOOLEAN | | Read status |
| created_at | TIMESTAMP | | Creation time |

## 8. Relationships

```text
USER 1 ---- N DOCUMENT
USER 1 ---- N LOAN
USER 1 ---- N NOTIFICATION
LOAN 1 ---- N INSTALLMENT
LOAN 1 ---- N PAYMENT
INSTALLMENT 1 ---- N PAYMENT
```

## 9. Important Constraints

### Maximum Loan

```text
amount <= 12,000,000
```

### Maximum Tenor

```text
tenor_months <= 12
```

### Existing Loan Rule

Before creating a loan, the Loan Service must check whether the user has an unresolved application or unpaid active loan.

Recommended rule:

```text
PENDING
APPROVED
ACTIVE
```

must block a new application.

`PAID_OFF`, `REJECTED`, and `CANCELLED` do not block a new application unless business rules say otherwise.
