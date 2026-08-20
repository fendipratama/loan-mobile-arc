# Screen Behavior — PT. XYZ Online Loan

## 1. Splash Screen

### On Open

1. Initialize application.
2. Load local authentication state.
3. Check access token.
4. If token is valid, navigate to Dashboard.
5. If token is expired, attempt refresh token.
6. If refresh fails, navigate to Login.

## 2. Login Screen

### Components

- Email/phone field
- Password field
- Login button
- Biometric button when supported
- Register link

### Behavior

- Disable Login when required fields are empty.
- Show validation error for invalid credentials.
- On successful login, save tokens securely and navigate to Dashboard.
- Biometric button is displayed only if device capability and user configuration allow it.

## 3. Register Screen

### Components

- Full name
- Email
- Phone
- Password
- Confirm password
- Continue button

### Validation

- Required fields cannot be empty.
- Email must have valid format.
- Phone must have valid format.
- Password must meet minimum security requirements.
- Password confirmation must match.

Successful registration proceeds to KYC upload.

## 4. KYC Upload Screen

### Components

- KTP upload/camera
- Selfie capture
- Preview
- Submit button

### Behavior

- Validate image format and size.
- Allow retake/re-upload.
- Display upload progress.
- Show KYC verification status.
- Do not expose private object-storage URLs directly to the user.

## 5. Dashboard

Display:

```text
Total Outstanding
Current Monthly Bill
Next Due Date
Loan Status
```

Primary actions:

```text
Ajukan Pinjaman
Lihat Tagihan
Riwayat Pinjaman
Notifikasi
```

If the user has an active/unresolved loan, the Apply Loan button should be disabled or replaced with a message explaining why the user cannot submit another application.

## 6. Loan Form

### Fields

- Loan amount
- Tenor

### Business Validation

```text
Minimum amount: Rp100.000
Maximum amount: Rp12.000.000
Maximum tenor: 12 months
```

The mobile application may perform client-side validation for UX, but the backend remains the authoritative validator.

### Behavior

If an unresolved loan exists:

```text
"Anda masih memiliki pengajuan/pinjaman yang belum selesai."
```

The user cannot continue.

## 7. Loan Confirmation

Show:

- Loan amount
- Tenor
- Estimated interest
- Estimated monthly installment
- Total repayment
- Terms and conditions

User must confirm before submission.

## 8. Loan Processing

After submission:

```text
Submitting
    |
    v
PENDING
    |
    v
Credit Scoring
    |
 +--+--+
 |     |
Approved Rejected
```

The mobile application should not assume the result immediately. It should obtain the authoritative status from the backend.

## 9. Loan Result

### Approved

Show:

- Approved amount
- Tenor
- Monthly installment
- Loan ID
- First due date

Notification should also be sent through configured channels.

### Rejected

Show:

- Rejected status
- Reason when business policy allows disclosure
- Next available action

## 10. Bills Screen

Display:

- Total outstanding
- Current bill
- Due date
- Installment number
- Payment status

Statuses:

```text
UNPAID
PARTIALLY_PAID
PAID
OVERDUE
```

## 11. Notification Screen

Display notification list ordered by newest first.

Notification examples:

- Loan application submitted.
- Loan approved.
- Loan rejected.
- Payment reminder.
- Payment successful.

Unread notifications are visually differentiated.

## 12. Profile Screen

Display:

- Name
- Email
- Phone
- KYC status
- Biometric setting
- Logout

## 13. Loading / Error / Empty States

Every API-driven screen should support:

### Loading

Show skeleton/progress indicator.

### Error

Show friendly error message and Retry action.

### Empty

Example:

```text
Belum ada riwayat pinjaman.
```

## 14. Security Behavior

- Tokens are stored using secure device storage.
- Logout removes local credentials/tokens.
- Sensitive data is not displayed unnecessarily.
- Screens containing financial information should be protected against unauthorized API access.
- Biometric authentication unlocks the application/session; it does not replace server-side authorization.
