---
share_cop4331c: "true"
site-folder: docs/Code Demos and Tutorials/LAMP Stack
---

# 📚 COP 4331 LAMP Stack Deployment & Development Tutorials

This tutorial series guides you through building, configuring, deploying, and testing the decoupled RESTful LAMP stack Colors Manager web application on a cloud virtual machine (DigitalOcean Droplet).

---

> [!IMPORTANT]
> **Domain Name Placeholder:**  
> Throughout these tutorials, **`lamp.johnaedo.com`** is used as the example domain and subdomain.  
> Whenever you see `lamp.johnaedo.com`, substitute it with the domain you acquired from your registrar (e.g., `lamp.yourdomain.com`).

---

## 📑 Tutorial Modules

| Module | Title | Topics Covered |
| :--- | :--- | :--- |
| **[Tutorial 1](./01-droplet-setup-and-dns.md#)** | **DigitalOcean Droplet Setup & DNS** | • Local SSH Key Generation (`ed25519` / `rsa`) on Windows & macOS<br>• Provisioning LAMP Droplet with root SSH authentication<br>• Connecting via Windows Terminal / macOS Terminal<br>• DNS setup via DigitalOcean DNS & Registrar `A` records |
| **[Tutorial 2](./02-database-setup-mysql.md#)** | **MySQL Database Setup & Seeding** | • Interacting with MySQL CLI (`sudo mysql`) on the Droplet<br>• Creating `ColorsAppDB`, `Users`, and `Colors` tables<br>• Least-privilege application user creation (`ColorsAppUser`)<br>• Executing SQL scripts (`create_tables.sql`, `seed_data.sql`, `resetdb.sql`) |
| **[Tutorial 3](./03-backend-api-deployment-and-testing.md#)** | **Backend RESTful API & Bruno Testing** | • PHP PDO REST API architecture (`api/index.php`, `db.php`, `helpers.php`)<br>• Deploying backend code to `/var/www/html/api`<br>• Environment configuration (`.env`) & Apache permissions<br>• Testing all endpoints with **Bruno** (Login, Search, Add, Update, Delete) |
| **[Tutorial 4](./04-frontend-integration-and-testing.md#)** | **Frontend Integration & Browser Testing** | • Reviewing `js/code.js` endpoints, cookies, and asynchronous AJAX<br>• Deploying `index.html`, `color.html`, `css/`, `js/` to the Droplet<br>• End-to-end browser walkthrough (Login, Session Cookies, Search, Add, Delete, Logout)<br>• Browser debugging with DevTools & optional SSL/Certbot setup |

---

## 🗄️ SQL Scripts Reference

The accompanying SQL scripts are located in the [`sql/`](../sql/) and [`api/config/`](../api/config/) directories:
- **[`sql/create_tables.sql`](../sql/create_tables.sql)**: Creates database schema and user permissions.
- **[`sql/seed_data.sql`](../sql/seed_data.sql)**: Populates sample users and color records.
- **[`sql/resetdb.sql`](../sql/resetdb.sql)**: All-in-one database reset and seed script.
