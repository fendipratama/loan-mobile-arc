# API Design — PT. XYZ Online Loan

## 1. Base URL

```text
/api/v1
```

Full OpenAPI specification is available in:

```text
api/swagger.json
```

## 2. Authentication

Protected endpoints use:

```http
Authorization: Bearer <access_token>
```

## 3. Endpoint Summary

| Method | Endpoint | Description |
|---|---|---|
| POST | `/auth/register` | Register user + upload KTP/selfie |
| POST | `/auth/login` | Login with password |
| POST | `/auth/biometric` | Biometric login |
| POST | `/auth/refresh` | Refresh access token |
| GET | `/users/me` | Get current user |
| PATCH | `/users/me/biometric` | Enable/disable biometric |
| POST | `/users/me/documents` | Upload KTP/selfie |
| GET | `/dashboard` | Get loan dashboard |
| POST | `/loans` | Submit loan application |
| GET | `/loans` | Get loan history |
| GET | `/loans/{loanId}` | Get loan detail |
| GET | `/loans/{loanId}/status` | Get loan status |
| GET | `/loans/{loanId}/installments` | Get installment schedule |
| GET | `/payments` | Get payment history |
| GET | `/notifications` | Get notifications |
| PATCH | `/notifications/{notificationId}/read` | Mark notification as read |

## 4. Register

```http
POST /api/v1/auth/register
Content-Type: multipart/form-data
```

Fields:

```text
fullName
email
phone
password
ktpImage
selfieImage
```

Expected result:

```text
201 Created
```

## 5. Login

```http
POST /api/v1/auth/login
Content-Type: application/json
```

Request:

```json
{
  "identifier": "user@example.com",
  "password": "Password123"
}
```

Response:

```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "accessToken": "<jwt>",
    "refreshToken": "<refresh-token>",
    "expiresIn": 3600
  }
}
```

## 6. Dashboard

```http
GET /api/v1/dashboard
Authorization: Bearer <token>
```

Example:

```json
{
  "hasActiveLoan": true,
  "remainingDebt": 5000000,
  "currentMonthlyBill": 500000,
  "nextDueDate": "2026-09-15"
}
```

## 7. Create Loan

```http
POST /api/v1/loans
Authorization: Bearer <token>
Content-Type: application/json
```

Request:

```json
{
  "amount": 10000000,
  "tenor": 12
}
```

Validation:

- `amount <= 12000000`
- `tenor <= 12`
- User must pass required KYC rules.
- User must not have another unresolved loan/application.

Possible responses:

```text
201 Created
400 Bad Request
401 Unauthorized
409 Conflict
422 Unprocessable Entity
```

## 8. Loan Status

```http
GET /api/v1/loans/{loanId}/status
Authorization: Bearer <token>
```

Status:

```text
PENDING
APPROVED
REJECTED
ACTIVE
PAID_OFF
CANCELLED
```

## 9. Installments

```http
GET /api/v1/loans/{loanId}/installments
Authorization: Bearer <token>
```

Example:

```json
{
  "items": [
    {
      "installmentNumber": 1,
      "dueDate": "2026-09-15",
      "amount": 1000000,
      "remainingAmount": 1000000,
      "status": "UNPAID"
    }
  ]
}
```

## 10. Error Response

Standard error:

```json
{
  "success": false,
  "code": "VALIDATION_ERROR",
  "message": "Invalid request",
  "errors": [
    {
      "field": "amount",
      "message": "Maximum loan amount is Rp12.000.000"
    }
  ],
  "traceId": "abc-123"
}
```

## 11. API Design Principles

- Versioned API: `/api/v1`.
- Consistent HTTP status codes.
- Consistent error response.
- Idempotency should be considered for loan submission and payment operations.
- Sensitive data must not be written to logs.
- API contract is maintained in OpenAPI/Swagger.
