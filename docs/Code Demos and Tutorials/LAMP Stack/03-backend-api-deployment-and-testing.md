---
share_cop4331c: "true"
site-folder: docs/Code Demos and Tutorials/LAMP Stack
---

# Tutorial 3: Backend RESTful API Architecture, Droplet Deployment & Testing with Bruno

In this third tutorial, you will deploy the backend RESTful PHP API to your DigitalOcean Droplet, connect it securely to the MySQL `ColorsAppDB` database, and execute comprehensive integration tests using the **Bruno** API client.

---

> [!IMPORTANT]
> **Domain Name Placeholder:**  
> Throughout this tutorial series, we use **`lamp.johnaedo.com`** as the example domain.  
> **You must replace `lamp.johnaedo.com` with the domain name you acquired from your domain registrar.**

---

## 🏗️ Backend Architecture Overview

The backend is organized into a clean, decoupled RESTful API written in PHP 8.1+ using **PHP Data Objects (PDO)** with parameterized queries to prevent SQL Injection vulnerabilities:

```
 api/
 ├── index.php         # REST Controller: Route dispatching, HTTP verb handling, JSON output
 └── config/
     ├── db.php        # PDO Database Singleton: Reads .env and manages MySQL connection
     ├── helpers.php   # Utility library: CORS headers, .env parser, JSON responses, requireAuth()
     └── resetdb.sql   # SQL database reset script
```

### Complete API Specification:

| HTTP Method | Endpoint | Headers / Auth | Request Body | Description |
| :--- | :--- | :--- | :--- | :--- |
| **`GET`** | `/api/index.php?ping=1` | None | None | Health check & uptime status |
| **`POST`** | `/api/index.php` | None | `{"login":"...", "password":"..."}` | Authenticates user; returns User ID & session token |
| **`GET`** | `/api/index.php` | `Authorization: Bearer <uid>` | None | Lists all colors belonging to authenticated user |
| **`GET`** | `/api/index.php?q=term` | `Authorization: Bearer <uid>` | None | Searches colors by partial substring match |
| **`GET`** | `/api/index.php?id=1` | `Authorization: Bearer <uid>` | None | Retrieves a single color record by ID |
| **`POST`** | `/api/index.php` | `Authorization: Bearer <uid>` | `{"color":"Name"}` | Creates a new color entry |
| **`PUT`** | `/api/index.php?id=1` | `Authorization: Bearer <uid>` | `{"color":"Name"}` | Updates an existing color name by ID |
| **`DELETE`** | `/api/index.php?id=1` | `Authorization: Bearer <uid>` | None | Deletes a color record by primary key ID |

---

## 🚀 Step 1: Deploy Backend Code to the Droplet


1. SSH into your Droplet:
   ```bash
   ssh root@<YOUR_DROPLET_IP>
   ```
2. Download the API files:
   ```bash
   curl -O https://teaching.johnaedo.com/code/cop4331c/lamp/api-files.zip
   ```

3. Navigate to `/var/www/html`:
   ```bash
   cd /var/www/html
   ```

4. Unzip the files:
   ```bash
   unzip /root/api-files.zip
   ```

---

## ⚙️ Step 2: Configure Environment Variables & Permissions

1. **Create the `.env` configuration file on the Droplet**:
   SSH into the Droplet and create `/var/www/html/.env`:
   ```bash
   nano /var/www/html/.env
   ```

2. Add your database connection parameters:
   ```ini
   DB_HOST=localhost
   DB_NAME=ColorsAppDB
   DB_USER=ColorsAppUser
   DB_PASSWORD=WeLoveCOP4331!
   DB_PORT=3306
   DB_CHARSET=utf8mb4
   ```
   *Press `Ctrl + O`, `Enter` to save, then `Ctrl + X` to exit `nano`.*

3. **Set Linux File Permissions**:
   Apache runs under the system user `www-data`. Ensure the web server has read/write permissions while protecting your secret `.env` file:
   ```bash
   # Set ownership to Apache's www-data user
   chown -R www-data:www-data /var/www/html

   # Set directory and file permissions
   # Directories are readable and executable by all, 
   # but only writable by www-data 
   find /var/www/html -type d -exec chmod 755 {} \;
   # Files are readable by all, but only writable by www-data 
   find /var/www/html -type f -exec chmod 644 {} \;

   # Restrict .env file permissions
   # only www-data can write the file
   chmod 600 /var/www/html/.env
   ```


---

## 🔍 Step 3: Verify API Health via cURL

Before opening Bruno, test the unauthenticated status ping endpoint directly from your local terminal:

```bash
curl -i http://lamp.johnaedo.com/api/index.php?ping=1
```

### Expected Response:
```http
HTTP/1.1 200 OK
Date: Mon, 24 Aug 2026 14:00:00 GMT
Server: Apache/2.4.52 (Ubuntu)
Content-Type: application/json; charset=utf-8

{"status":"OK","timestamp":1787580000}
```

If you receive HTTP `200 OK` with JSON, Apache and PHP are communicating correctly!

---

## 🧪 Step 4: Testing the API with Bruno

[Bruno](https://www.usebruno.com/) is an open-source, fast, Git-friendly desktop API client.

### A. Install and Open Bruno on your local PC
1. Download Bruno from [usebruno.com](https://www.usebruno.com/downloads) or install via package manager:
   - **macOS**: `brew install bruno`
   - **Windows**: `winget install Bruno.Bruno`
2. Launch the Bruno desktop application.

### B. Download the API tests

You'll want to download the test files to your local computer, where you installed Bruno.

> [! FILES DOWNLOAD]
> [bruno-tests.zip](https://teaching.johnaedo.com/code/cop4331c/lamp/bruno-tests.zip)

Unzip the files into the directory of your choice.

### C. Open the Project Collection in Bruno
1. In Bruno, click **Open Collection** button in the Overview tab.
2. Browse to the folder you just unzipped called `api-tests` (yes, that's different than the zip file name)
3. The collection **LAMP Demo** will load in the sidebar.

```
📁 api-tests (LAMP Demo)
├── 📄 Status Ping
├── 📄 Login
├── 📄 Invalid Login
├── 📄 Search
├── 📄 Add
├── 📄 Get Color
├── 📄 Update Color
└── 📄 Delete Color
```

### D. Configure the Production Environment
1. Select the `Status Ping` test.
2. In the top-right corner of Bruno, click the environment dropdown (currently set to *No Environment*).
3. Select **Configure**
4. Select **Production**
5. Verify that the `urlBase` variable is set to your domain:
   ```yaml
   name: Production
   variables:
     - name: urlBase
       value: http://lamp.johnaedo.com
   ```
   *(Replace `http://lamp.johnaedo.com` with your actual domain URL).*
6. Select **Save**

---

### D. Step-by-Step API Endpoint Tests

Execute each request sequentially in Bruno:

#### 1. Status Ping
- **Method**: `GET`
- **URL**: `{{urlBase}}/api/index.php?ping=1`
- **Click**: **Send** (or `Ctrl+Enter` / `Cmd+Enter`)
- **Expected Status**: `200 OK`
- **Expected Body** (timestamp will differ in value):
  ```json
  {
    "status": "OK",
    "timestamp": 1787580000
  }
  ```

---

#### 2. User Login (Success)
- **Method**: `POST`
- **URL**: `{{urlBase}}/api/index.php`
- **Body (JSON)**:
  ```json
  {
    "login": "RickL",
    "password": "COP4331"
  }
  ```
- **Click**: **Send**
- **Expected Status**: `200 OK`
- **Expected Body**:
  ```json
  {
    "id": 1,
    "firstName": "Rick",
    "lastName": "Leinecker",
    "token": "1",
    "error": ""
  }
  ```
  *Note the `id: 1` returned. This ID represents the authenticated session token.*

---

#### 3. User Login (Failure / Bad Credentials)
- **Method**: `POST`
- **URL**: `{{urlBase}}/api/index.php`
- **Body (JSON)**:
  ```json
  {
    "login": "RickL",
    "password": "WRONG_PASSWORD"
  }
  ```
- **Click**: **Send**
- **Expected Status**: `401 Unauthorized`
- **Expected Body**:
  ```json
  {
    "id": 0,
    "firstName": "",
    "lastName": "",
    "error": "No Records Found"
  }
  ```

---

#### 4. Search Colors
- **Method**: `GET`
- **URL**: `{{urlBase}}/api/index.php?q=blue`
- **Headers**:
  - `Authorization`: `Bearer 1`
- **Click**: **Send**
- **Expected Status**: `200 OK`
- **Expected Body**:
  ```json
  {
    "results": [
      "Blue",
      "Light Blue"
    ],
    "colors": [
      { "id": 1, "name": "Blue" },
      { "id": 10, "name": "Light Blue" }
    ],
    "error": ""
  }
  ```

---

#### 5. Add Color
- **Method**: `POST`
- **URL**: `{{urlBase}}/api/index.php`
- **Headers**:
  - `Authorization`: `Bearer 1`
  - `Content-Type`: `application/json`
- **Body (JSON)**:
  ```json
  {
    "color": "Light Chartreuse"
  }
  ```
- **Click**: **Send**
- **Expected Status**: `201 Created`
- **Expected Body** (id may be different in your instance):
  ```json
  {
    "message": "Color created",
    "id": 35,
    "error": ""
  }
  ```
  *Take note of the returned `id` (e.g., `35`).*

---

#### 6. Get Color (gets a single color by ID)

Make sure to use the ID returned in the previous step.

- **Method**: `GET`
- **URL**: `{{urlBase}}/api/index.php?id=35`
- **Headers**:
  - `Authorization`: `Bearer 1`
- **Click**: **Send**
- **Expected Status**: `200 OK`
- **Expected Body**:
  ```json
  {
    "id": 35,
    "name": "Light Chartreuse",
    "user_id": 1
  }
  ```

---

#### 7. Update Color
- **Method**: `PUT`
- **URL**: `{{urlBase}}/api/index.php?id=35`
- **Headers**:
  - `Authorization`: `Bearer 1`
  - `Content-Type`: `application/json`
- **Body (JSON)**:
  ```json
  {
    "color": "Electric Lime"
  }
  ```
- **Click**: **Send**
- **Expected Status**: `200 OK`
- **Expected Body**:
  ```json
  {
    "message": "Color updated",
    "error": ""
  }
  ```
You can go run the `Get Color` test with an ID of 1 to verify that the color updated.

---

#### 8. Delete Color
- **Method**: `DELETE`
- **URL**: `{{urlBase}}/api/index.php?id=35`
- **Headers**:
  - `Authorization`: `Bearer 1`
- **Click**: **Send**
- **Expected Status**: `200 OK`
- **Expected Body**:
  ```json
  {
    "message": "Color deleted",
    "error": ""
  }
  ```

---

## 🛠️ Step 5: Troubleshooting API & Server Errors

If an endpoint returns an unexpected error:

1. **Check Apache Error Logs**:
   ```bash
   tail -n 50 -f /var/log/apache2/error.log
   ```
2. **Database Connection Refused (HTTP 500)**:
   - Check if MySQL is running: `systemctl status mysql`
   - Verify credentials in `/var/www/html/.env`.
   - Test connecting with MySQL CLI using `ColorsAppUser`:
     ```bash
     mysql -u ColorsAppUser -p'WeLoveCOP4331!' -D ColorsAppDB
     ```
3. **Missing Authorization Header**:
   - In Apache, `Authorization` headers may sometimes be stripped by default. Ensure `a2enmod rewrite` is enabled. `api/config/helpers.php` also supports `X-User-Id` header and cookies as fallbacks.

---

## 🎯 Summary & Next Steps

In this tutorial, you accomplished the following:
- Deployed the PHP RESTful API backend files to `/var/www/html/api`.
- Configured environment variables in `.env` and set Linux file permissions.
- Verified live server health using `curl`.
- Imported the collection into Bruno and thoroughly tested authentication, query filtering, insert, update, and delete actions against `http://lamp.johnaedo.com`.

In **[Tutorial 4: Frontend Integration & Browser Testing](./04-frontend-integration-and-testing.md#)**, you will update the client-side JavaScript, deploy the HTML/CSS interface, and test the full application workflow directly in your web browser.
