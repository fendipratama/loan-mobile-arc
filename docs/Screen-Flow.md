# Screen Flow — PT. XYZ Online Loan

## 1. Main Flow

```text
Splash
  |
  +-- Token Valid? -- YES --> Dashboard
  |                 |
  |                 NO
  |                 v
  +--------------> Login
                     |
              +------+------+
              |             |
           Login         Register
              |             |
              |        Personal Data
              |             |
              |        Upload KTP/Selfie
              |             |
              |        KYC Verification
              |             |
              +-------> Dashboard
                           |
          +----------------+----------------+
          |                |                |
       Dashboard        Loan Menu        Profile
                           |
                    Existing Loan?
                      /          \
                    YES          NO
                     |            |
                  Block        Loan Form
                                  |
                         Amount + Tenor
                                  |
                              Confirmation
                                  |
                              Submit
                                  |
                             Processing
                                  |
                       +----------+----------+
                       |                     |
                    Approved              Rejected
                       |                     |
                   Create Loan          Show Reason
                       |
                Generate Installment
                       |
                Notification
                       |
                   Dashboard
```

## 2. Navigation

```text
Dashboard
├── Loan
│   ├── Apply Loan
│   ├── Loan Status
│   └── Loan History
├── Bills
│   ├── Current Bill
│   └── Payment History
├── Notifications
└── Profile
    ├── Personal Information
    ├── KYC Status
    └── Biometric Setting
```

## 3. Screen Inventory

| Screen | Purpose |
|---|---|
| Splash | Initialize application/session |
| Login | Authenticate user |
| Register | Create user account |
| KYC Upload | Upload KTP and selfie |
| Dashboard | Show financial summary |
| Loan Form | Input loan amount and tenor |
| Loan Confirmation | Review application |
| Loan Processing | Show pending state |
| Loan Result | Approved/rejected result |
| Loan Detail | Show active loan |
| Bills | Show monthly installments |
| Payment History | Show payments |
| Notifications | Show system notifications |
| Profile | Manage account |
| Biometric Setting | Manage biometric login |

## 4. Navigation Rules

- Unauthenticated user cannot access protected screens.
- User with an unresolved loan can view loan detail but cannot create another loan.
- Loan application status must be refreshed from backend.
- Logout clears local authentication/session data.
