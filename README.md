# 🏦 Bank of Bangalore

> A full-stack digital banking application built with Flask, SQLite, SQLAlchemy, and a custom JSON-over-TCP transaction service.

Bank of Bangalore is a local digital banking simulation that provides separate customer and administrator portals, account management, secure authentication, fund transfers, transaction history, statements, notifications, audit logging, reporting, and TCP server monitoring.

The application combines a Flask web application with a custom TCP banking service. The Flask layer handles the web experience and user workflows, while the TCP layer exposes core banking operations such as balance inquiries, transfers, statements, transaction history, and server statistics.

---

## ✨ Features

### 👤 Customer Portal

- Customer registration with validation.
- Login and logout using Flask-Login.
- Password hashing with Werkzeug.
- Password-strength validation.
- Account creation with pending approval status.
- Account balance and account-status views.
- Fund transfers between bank accounts.
- Transaction PIN verification before transfers.
- Failed PIN-attempt protection and temporary transfer lockout.
- Saved beneficiaries.
- Mini statements.
- Full transaction history with:
  - Search
  - Transaction-type filtering
  - Status filtering
  - Sorting
  - Pagination
- Transaction receipt pages.
- PDF transaction-receipt generation.
- PDF and CSV statement downloads.
- Customer profile management.
- Password and transaction-PIN management.
- PIN reset using the account password.
- Security and transfer notifications.
- Login, logout, transfer, and security audit events.

### 🛡️ Admin / Banker Portal

- Admin dashboard with banking metrics.
- Customer search and account-status filtering.
- Customer profile editing.
- Customer account approval.
- Account activation and freezing.
- Account unfreezing.
- Customer deletion.
- Account search and management.
- Transaction search and filtering.
- Transaction CSV export.
- Daily transaction reports.
- Monthly transaction reports.
- Transfer-volume reports.
- Frozen-account reports.
- Inactive-account reports.
- Customer transaction-volume reports.
- Audit-log viewer with event filters and pagination.
- TCP server monitoring.
- TCP connection/request/transaction statistics.
- Admin profile settings.
- Broadcast announcements to customers.

### 🔐 Authentication & Security

- Flask-Login session authentication.
- Werkzeug password hashing.
- Password requirements:
  - Minimum 8 characters
  - At least one uppercase letter
  - At least one lowercase letter
  - At least one digit
- Login lockout after repeated failed password attempts.
- Transaction-PIN hashing.
- Three-attempt PIN lockout for transfers.
- Flask-WTF CSRF protection.
- Role checks separating customer and administrator functionality.
- Strong Flask-Login session protection.
- Audit logging of important security and administrative actions.

### 🌐 Custom TCP Banking Service

The project contains a separate TCP server using Python sockets and newline-delimited JSON.

Supported actions include:

| Action | Purpose |
|---|---|
| `login` | Authenticate a banking user |
| `balance` | Retrieve account balance and account information |
| `transfer` | Transfer funds between accounts |
| `mini_statement` | Retrieve recent transactions |
| `history` | Retrieve paginated transaction history |
| `server_stats` | Retrieve TCP server statistics |

The Flask application communicates with the TCP service through `tcp_client.py`.

Transfers use an atomic SQL update that checks the sender's available balance as part of the update, helping prevent concurrent requests from spending the same funds.

---

## 🏗️ Architecture

```text
                         ┌─────────────────────────┐
                         │       Web Browser       │
                         └────────────┬────────────┘
                                      │ HTTP
                                      ▼
                         ┌─────────────────────────┐
                         │      Flask Web App      │
                         │                         │
                         │  Auth / Customer/Admin  │
                         │      Route Blueprints   │
                         └────────────┬────────────┘
                                      │
                         ┌────────────┴────────────┐
                         │                         │
                         ▼                         ▼
              ┌──────────────────┐       ┌──────────────────┐
              │   SQLAlchemy     │       │    TCP Client    │
              │      ORM         │       │   tcp_client.py  │
              └────────┬─────────┘       └────────┬─────────┘
                       │                          │ JSON/TCP
                       ▼                          ▼
              ┌──────────────────┐       ┌──────────────────┐
              │   SQLite bank.db │       │   TCP Server     │
              │                  │       │   port 9999      │
              │ Users            │       │                  │
              │ Accounts         │       │ login            │
              │ Transactions     │       │ balance          │
              │ Beneficiaries    │       │ transfer         │
              │ Notifications    │       │ statements       │
              │ Audit Logs       │       │ history          │
              └──────────────────┘       │ server_stats     │
                                         └──────────────────┘
```

### Application startup

`app.py` creates the Flask application, initializes SQLAlchemy, Flask-Login, and CSRF protection, registers the authentication/customer/admin blueprints, creates the database tables, and seeds the database when required.

When the application is started directly, the TCP server is launched in a background daemon thread alongside the Flask server.

---

## 🧰 Technology Stack

### Backend

- Python
- Flask 3.1.1
- Flask-Login 0.6.3
- Flask-SQLAlchemy 3.1.1
- Flask-WTF 1.2.2
- Werkzeug 3.1.3
- ReportLab 4.4.0

### Database

- SQLite
- SQLAlchemy ORM

### Frontend

- Jinja2 templates
- HTML5
- Tailwind CSS via CDN
- Custom CSS
- Vanilla JavaScript
- Google Fonts / Inter

### Networking

- Python `socket`
- JSON-over-TCP
- Multithreaded TCP request handling

---

## 📁 Project Structure

```text
bank-of-bangalore/
│
├── app.py
├── config.py
├── models.py
├── seed.py
├── tcp_client.py
├── tcp_server.py
├── concurrency_test.py
├── requirements.txt
├── README.md
│
├── database/
│   └── bank.db
│
├── routes/
│   ├── auth.py
│   ├── customer.py
│   └── admin.py
│
├── templates/
│   ├── base.html
│   ├── base_auth.html
│   ├── base_admin.html
│   │
│   ├── auth/
│   │   ├── login.html
│   │   ├── register.html
│   │   ├── forgot_password.html
│   │   └── change_password.html
│   │
│   ├── customer/
│   │   ├── dashboard.html
│   │   ├── accounts.html
│   │   ├── balance.html
│   │   ├── transfer.html
│   │   ├── transfer_success.html
│   │   ├── receipt.html
│   │   ├── mini_statement.html
│   │   ├── transaction_history.html
│   │   ├── beneficiaries.html
│   │   ├── notifications.html
│   │   ├── profile.html
│   │   ├── edit_profile.html
│   │   ├── security.html
│   │   └── reset_pin.html
│   │
│   └── admin/
│       ├── dashboard.html
│       ├── customers.html
│       ├── edit_customer.html
│       ├── accounts.html
│       ├── transactions.html
│       ├── reports.html
│       ├── audit_logs.html
│       ├── tcp_monitor.html
│       └── settings.html
│
├── static/
│   ├── css/
│   │   └── custom.css
│   └── js/
│       └── main.js
│
└── docs/
```

> The `database/bank.db` file is created/used by the application. The seed process can populate a fresh database with demonstration data.

---

## 🗃️ Data Model

The application currently defines the following SQLAlchemy models:

### `User`

Stores customer and administrator identity and authentication data.

Important fields include:

- ID
- Name
- Email
- Phone
- Password hash
- Role
- Address
- Security question/answer hash
- Failed login attempts
- Account lock timestamp
- Transaction PIN hash
- Failed PIN attempts
- PIN lock timestamp
- Default-PIN flag
- Creation timestamp

### `Account`

Represents a bank account linked to a user.

- Account number
- Account type (`savings` / `current`)
- Balance
- Status (`pending`, `active`, `frozen`, `closed`)
- Creation timestamp

### `Transaction`

Stores banking transaction records.

- Transaction ID
- Sender account
- Receiver account
- Transaction type
- Amount
- Description
- Status
- Creation timestamp

### `Beneficiary`

Stores a customer's saved transfer recipients.

### `Notification`

Stores customer notifications such as:

- Account approvals
- Transfers
- Security events
- Announcements

### `Log`

Stores audit events such as:

- Login
- Logout
- Transfers
- Administrative actions
- Security events
- System events

---

## 🔄 Main Workflows

### Customer registration

```text
Registration Form
      │
      ▼
Validate fields
      │
      ▼
Validate password + PIN
      │
      ▼
Hash credentials
      │
      ▼
Create customer
      │
      ▼
Create pending account
      │
      ▼
Create notification + audit log
      │
      ▼
Wait for admin approval
```

### Fund transfer

```text
Customer
   │
   ▼
Transfer Form
   │
   ├── Validate account
   ├── Validate amount
   ├── Validate account status
   └── Validate transaction PIN
          │
          ▼
     Flask TCP Client
          │
          ▼
     TCP JSON Request
          │
          ▼
      TCP Server
          │
          ▼
Atomic balance update
          │
          ├── Debit sender
          ├── Credit receiver
          ├── Create transaction records
          └── Create audit log
          │
          ▼
      JSON Response
          │
          ▼
Customer notification
```

### Account approval

```text
Customer registers
       │
       ▼
Account = pending
       │
       ▼
Admin reviews customer
       │
       ▼
Admin approves
       │
       ▼
Account = active
       │
       ▼
Customer receives notification
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10+ recommended
- `pip`
- Git

### 1. Create a virtual environment

#### Windows

```powershell
python -m venv venv
venv\Scripts\activate
```

#### Linux / macOS

```bash
python3 -m venv venv
source venv/bin/activate
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure the application

The application reads configuration from environment variables where applicable.

The main settings include:

| Setting | Default |
|---|---|
| `SECRET_KEY` | Development fallback defined in `config.py` |
| Database | `database/bank.db` |
| Web server | `0.0.0.0:5000` |
| TCP server | `0.0.0.0:9999` |
| Session lifetime | 30 minutes |
| Maximum failed logins | 5 |
| Login lockout | 30 minutes |
| CSRF | Enabled |

For anything beyond local/demo use, set a strong `SECRET_KEY` through the environment instead of relying on the development fallback.

### 4. Start the application

```bash
python app.py
```

The web application will be available at:

```text
http://127.0.0.1:5000
```

The TCP service starts alongside the Flask application on:

```text
0.0.0.0:9999
```

The root URL redirects users to the login page.

---

##  Database Seeding

A fresh database is automatically created when the application starts.

The seed script creates:

- A default administrator.
- 300 demonstration customers.
- One account for each generated customer.
- Approximately 60–80 transactions per customer.
- Sample notifications.
- An initial system audit log.

To force reseeding:

```powershell
$env:SEED_DB="1"
python app.py
```

> **Warning:** reseeding should be treated as a development/demo operation. Do not use seeded credentials or demonstration data in a real banking environment.

The seed script currently contains demonstration credentials for local development. Change or remove them before deploying anywhere outside a controlled test environment.

---

## 👤 Demo Accounts

The seed script creates the administrator account:

```text
Email:    admin@bankofpattanagere.com
Password: admin123
Role:     admin
```

It also generates customer accounts dynamically using deterministic demo password patterns based on the generated first name.

Because these credentials are embedded in the seed implementation, they are intended only for local demonstration/testing.

---

## 🌐 Web Routes

### Authentication

| Method | Route | Purpose |
|---|---|---|
| GET/POST | `/auth/login` | Login |
| GET/POST | `/auth/register` | Customer registration |
| GET | `/auth/logout` | Logout |
| GET/POST | `/auth/forgot-password` | Password recovery |
| GET/POST | `/auth/change-password` | Authenticated password change |

### Customer

| Route | Purpose |
|---|---|
| `/customer/dashboard` | Customer dashboard |
| `/customer/accounts` | Account details |
| `/customer/balance` | Balance inquiry |
| `/customer/transfer` | Fund transfer |
| `/customer/transfer/success/<transaction_id>` | Transfer confirmation |
| `/customer/receipt/<transaction_id>` | Transaction receipt |
| `/customer/receipt/<transaction_id>/pdf` | PDF receipt |
| `/customer/mini-statement` | Recent transactions |
| `/customer/download-statement/<fmt>` | PDF/CSV statement |
| `/customer/transactions` | Searchable transaction history |
| `/customer/beneficiaries` | Saved beneficiaries |
| `/customer/beneficiaries/add` | Add beneficiary |
| `/customer/beneficiaries/delete/<id>` | Remove beneficiary |
| `/customer/notifications` | Notifications |
| `/customer/profile` | Profile |
| `/customer/profile/edit` | Edit profile |
| `/customer/security` | Password/PIN settings |
| `/customer/security/reset-pin` | Reset transaction PIN |

### Admin

| Route | Purpose |
|---|---|
| `/admin/dashboard` | Admin dashboard |
| `/admin/customers` | Customer management |
| `/admin/customers/<id>/edit` | Edit customer |
| `/admin/customers/<id>/approve` | Approve account |
| `/admin/customers/<id>/activate` | Activate account |
| `/admin/customers/<id>/deactivate` | Freeze customer account |
| `/admin/customers/<id>/delete` | Delete customer |
| `/admin/accounts` | Account management |
| `/admin/accounts/<id>/freeze` | Freeze account |
| `/admin/accounts/<id>/unfreeze` | Unfreeze account |
| `/admin/transactions` | Transaction management |
| `/admin/transactions/export` | Export transactions as CSV |
| `/admin/reports` | Banking reports |
| `/admin/audit-logs` | Audit logs |
| `/admin/tcp-monitor` | TCP server monitor |
| `/admin/settings` | Admin profile settings |
| `/admin/announce` | Broadcast customer announcement |

---

## 🔌 TCP Protocol

The TCP service uses newline-delimited JSON.

### Request format

```json
{
  "action": "balance",
  "account": "101560001000001"
}
```

### Example transfer request

```json
{
  "action": "transfer",
  "from": "101560001000001",
  "to": "101560001000002",
  "amount": 5000,
  "remarks": "Fund Transfer"
}
```

### Example response

```json
{
  "status": "success",
  "transaction_id": "TXN100001",
  "amount": 5000,
  "sender_balance": 45000,
  "receiver_name": "Example Customer",
  "receiver_account": "101560001000002",
  "timestamp": "2026-01-01 12:00:00"
}
```

The TCP client automatically connects to `127.0.0.1` when the configured TCP host is `0.0.0.0`.

---

## 🧪 Concurrency Testing

The repository includes `concurrency_test.py` to exercise simultaneous transfers against the TCP transaction service.

The test:

1. Creates an isolated SQLite test database.
2. Creates two test accounts.
3. Starts the TCP server on port `9998`.
4. Fires simultaneous transfer requests using multiple threads.
5. Checks the final sender balance.
6. Verifies that the sender balance does not become negative.
7. Tests whether only one transfer succeeds when several simultaneous requests compete for the same available funds.

Run it with:

```bash
python concurrency_test.py
```

The test includes scenarios with:

- 2 simultaneous transfers.
- 5 simultaneous transfers.

---

## 📄 Statements & PDF Generation

The application uses ReportLab to generate:

- Transaction receipts in PDF format.
- Mini statements in PDF format.

CSV exports are generated directly by the Flask routes.

Customer statements are limited to the latest 10 transactions in the mini-statement/download workflow, while the full transaction-history page provides pagination and filtering.

---

## 🎨 User Interface

The UI is built with Jinja2 templates and Tailwind CSS loaded through the Tailwind CDN.

The project provides separate layouts for:

- Authentication pages.
- Customer portal.
- Administrator portal.

The customer interface includes responsive navigation, account-status indicators, notifications, dashboard cards, transfer forms, transaction tables, and security settings.

The admin interface provides a separate administration navigation and dashboards for customer/account management, reports, audit logs, and TCP monitoring.

---

## 🔒 Security Notes

This project is a banking-system simulation and should **not** be treated as production banking software without substantial additional security and infrastructure work.

Important considerations before production deployment include:

- Replace the development `SECRET_KEY`.
- Remove hard-coded demonstration credentials.
- Use HTTPS/TLS.
- Protect or replace the raw TCP service with authenticated and encrypted transport.
- Use a production-grade database such as PostgreSQL.
- Use `Decimal`/fixed-precision monetary arithmetic rather than floating-point balances.
- Add stronger authorization and ownership checks around every financial operation.
- Add database migrations.
- Add automated unit/integration/security tests.
- Add rate limiting and abuse protection.
- Add structured security monitoring.
- Protect sensitive logs and avoid storing unnecessary personal information.
- Review CSRF protection for every state-changing browser workflow.
- Add transaction idempotency and stronger transaction identifiers.
- Use proper database-level constraints and transactional guarantees.
- Run behind a production WSGI server/reverse proxy rather than Flask's development server.
- Store secrets outside source control.

---

## ⚠️ Project Limitations

The current implementation is designed as an educational/demo banking platform.

Some implementation characteristics to be aware of:

- SQLite is used as the primary database.
- Monetary balances are represented using Python/SQLAlchemy `Float`.
- The TCP service uses plain JSON over TCP rather than TLS.
- The development configuration contains a fallback secret key.
- Seed data contains demo credentials.
- Database schema creation uses `db.create_all()` rather than a migration framework.
- The application runs Flask's development server when launched directly.
- The TCP server is started as a daemon thread by the Flask application.
- Some reports calculate metrics in application code and may become expensive as data grows.
- There is no external payment gateway or real banking network integration.

---

## 🛠️ Development

Useful files when extending the project:

| File | Responsibility |
|---|---|
| `app.py` | Flask application factory and startup |
| `config.py` | Application configuration |
| `models.py` | SQLAlchemy models |
| `seed.py` | Development/demo data generation |
| `routes/auth.py` | Authentication workflows |
| `routes/customer.py` | Customer workflows |
| `routes/admin.py` | Admin workflows |
| `tcp_server.py` | Banking TCP server |
| `tcp_client.py` | Flask-to-TCP communication |
| `concurrency_test.py` | Concurrent transfer testing |
| `templates/` | Jinja2 UI |
| `static/css/custom.css` | Custom styling |
| `static/js/main.js` | Frontend behavior |

---

## 📊 Core Domain Entities

```text
User
 │
 ├── 1 ─── 1 Account
 │
 ├── 1 ─── N Beneficiary
 │
 ├── 1 ─── N Notification
 │
 └── 1 ─── N Log

Account
 │
 └── participates in Transactions
       ├── sender_account
       └── receiver_account
```

---
