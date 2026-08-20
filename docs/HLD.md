# High Level Design — PT. XYZ Online Loan Mobile Apps

## 1. Overview

PT. XYZ membutuhkan aplikasi mobile fintech lending yang memungkinkan pengguna melakukan registrasi, KYC, login, melihat tagihan, mengajukan pinjaman, dan menerima notifikasi hasil pengajuan.

Arsitektur yang direkomendasikan adalah **Microservices Architecture** dengan API Gateway sebagai entry point dari mobile application.

## 2. Business Rules

1. User wajib melakukan registrasi menggunakan nama, email, nomor telepon, password, foto selfie, dan KTP.
2. User dapat login menggunakan password.
3. User dapat menggunakan biometric login apabila perangkat mendukung dan user telah mengaktifkannya.
4. Maksimum pinjaman adalah **Rp12.000.000**.
5. Maksimum tenor adalah **12 bulan**.
6. User tidak dapat mengajukan pinjaman baru jika masih memiliki pengajuan yang sedang diproses atau pinjaman yang belum lunas.
7. Setiap pengajuan akan melalui proses eligibility/credit scoring.
8. Pengajuan menghasilkan status **PENDING**, **APPROVED**, atau **REJECTED**.
9. Jika disetujui, sistem membuat loan dan jadwal cicilan.
10. Perubahan status penting dikirim melalui push notification, email, dan/atau SMS.

## 3. High Level Architecture

```text
+----------------------+
|    Mobile Apps       |
| Android / iOS        |
+----------+-----------+
           |
         HTTPS
           |
+----------v-----------+
|      API Gateway     |
| Auth / Rate Limit    |
+----------+-----------+
           |
   +-------+--------+----------------+----------------+
   |                |                |                |
+--v---+        +---v---+        +---v---+        +---v------+
| Auth |        | User  |        | Loan  |        | Payment  |
|Svc   |        | Svc   |        | Svc   |        | Service  |
+--+---+        +---+---+        +---+---+        +----+-----+
   |                |                |                   |
 Auth DB         User DB          Loan DB            Payment DB
                    |
                Document
                 Storage

           +----------------------+
           |   Credit Scoring     |
           +----------+-----------+
                      |
           +----------v-----------+
           | Message Broker       |
           | Kafka / RabbitMQ     |
           +----------+-----------+
                      |
             +--------+--------+
             |                 |
       Notification       Audit/Logging
       Email/SMS/Push      & Monitoring
```

## 4. Components

### Mobile Application
Responsible for UI, local session management, biometric capability, validation, and API communication.

### API Gateway
Responsible for routing, authentication enforcement, rate limiting, request correlation, and API versioning.

### Authentication Service
Handles registration credentials, login, JWT access token, refresh token, and biometric credential registration.

### User/KYC Service
Handles user profile, KTP, selfie, and KYC verification status.

### Loan Service
Handles loan application, business rules, credit scoring orchestration, approval/rejection, and loan lifecycle.

### Payment Service
Handles installment schedules, outstanding balance, payment records, and payment status.

### Notification Service
Sends push notification, email, and SMS for loan status and payment reminders.

### Document Storage
Stores KTP/selfie files using object storage such as MinIO or S3.

### Message Broker
Decouples asynchronous operations such as notifications and audit events.

### Database
Each service owns its data where practical to reduce coupling.

## 5. Recommended Technology

| Layer | Recommendation |
|---|---|
| Mobile | Flutter |
| Backend | Go + Fiber |
| API Specification | OpenAPI / Swagger |
| Database | PostgreSQL / SQL Server |
| Cache | Redis |
| Message Broker | Kafka / RabbitMQ |
| Object Storage | MinIO / S3 |
| Authentication | JWT + Refresh Token |
| Container | Docker |
| Orchestration | Kubernetes |
| Monitoring | Prometheus + Grafana |
| Logging | OpenSearch/ELK |
| Tracing | OpenTelemetry |

## 6. Security

- HTTPS/TLS for all API communication.
- Password stored using strong password hashing such as Argon2id/bcrypt.
- Short-lived access token and refresh token rotation.
- Rate limiting on authentication endpoints.
- KTP/selfie files stored in private object storage.
- Authorization checks on every protected resource.
- Audit logging for authentication and loan lifecycle events.
- Do not store raw biometric data in the application database.
- Encrypt sensitive data at rest where applicable.

## 7. Scalability

Stateless backend services allow horizontal scaling behind the API Gateway/load balancer. Redis can be used for short-lived cache/session-related data, while the message broker handles asynchronous notification and integration workloads.

## 8. Observability

Every request should have a correlation/trace ID. Important metrics include API latency, error rate, login failure rate, loan processing time, notification delivery rate, and payment processing status.
