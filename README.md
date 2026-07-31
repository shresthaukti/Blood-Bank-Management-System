# HemoLink — Blood Bank Management System

HemoLink is a full-stack web application for managing blood bank operations — donor
registration, blood unit inventory, hospital requests, and the approval/transfusion
workflow that connects them. It's built with plain PHP (PDO) on top of PostgreSQL, with a
JSON API layer used by the front-end pages for dynamic interactions.

---
## Visit Our Website
Scan the QR below:

![Website QR Code](images/hemolink-website.png)
---

## Features

### Donor Portal
- Self-service registration and login
- Editable profile (name, contact info, address, gender, DOB)
- Full donation history with per-unit status (Available, Reserved, Transfused, Expired, Discarded)

### Staff / Admin Portal
- **Dashboard** — live counts of donors, available units, requests by status, urgent pending
  requests, and units expiring within 7 days
- **Inventory Stock** — filterable list of all blood units (by group, component, bank, status)
  with inline status updates and deletion
- **Add Stock** — record a new donation, which automatically computes an expiry date based on
  component type and updates donor stats
- **Expiry Tracker** — surfaces units that are expired or expiring within 7 days, with
  one-click "Mark Expired" / "Discard" actions
- **Blood Requests** — hospitals' requests can be submitted, searched, and filtered by status
- **Request Approval** — reviewing a request auto-matches compatible, non-expired units
  (using blood-group compatibility rules), reserves them, and tracks the transfusion outcome
  through to fulfillment or cancellation

### Core Domain Rules
- Component-specific expiry windows: Whole Blood (35d), RBC (42d), Plasma (365d),
  Platelets (5d), Cryoprecipitate (365d)
- Blood units auto-flip to `Expired` on update if past their expiry date (DB trigger)
- O− stock is treated as the universal-donor reserve, with low-stock warnings before it's
  allocated to non-urgent, non-O− requests

---

## Tech Stack

| Layer     | Technology                          |
|-----------|--------------------------------------|
| Language  | PHP 8+ (`declare(strict_types=1)`, PDO) |
| Database  | PostgreSQL                          |
| Frontend  | Server-rendered HTML/CSS + vanilla JS (fetch API) |
| API       | JSON endpoints under `/api` (CORS-enabled) |

---


## Setup

### 1. Requirements
- PHP 8.0+ with the `pdo_pgsql` extension enabled
- PostgreSQL 12+
- A web server (Apache/Nginx) or `php -S localhost:8000` for local development

### 2. Create the database

```bash
createdb blood_bank
psql -d blood_bank -f database.sql
```

### 3. Configure credentials

Copy the example config and fill in your own database credentials. `config.php` is
gitignored on purpose — never commit real credentials.

```php
<?php
define('DB_HOST', 'localhost');
define('DB_PORT', 5432);
define('DB_NAME', 'blood_bank');
define('DB_USER', 'your_db_user');
define('DB_PASS', 'your_db_password');
define('BASE_URL', '/');
?>
```

### 4. Run it

```bash
php -S localhost:8000
```

Then visit `http://localhost:8000/index.php`.

### 5. Log in

- **Donors** can self-register via `register.php`.
- **Staff/Admin** accounts must be seeded directly into `app_user` (with a
  `password_hash()`-generated hash) since there's no public staff sign-up flow.

---

## Security Notes

- Passwords are stored using PHP's `password_hash()` / verified with `password_verify()`.
- All database queries use prepared statements (PDO) to prevent SQL injection.
- Access control is centralized in `includes/auth.php` via `require_login($roles)`,
  used on every protected page and API endpoint.
- `config.php` (real DB credentials) is excluded from version control via `.gitignore`.

