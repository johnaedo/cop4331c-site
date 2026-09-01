---
share_cop4331c: "true"
site-folder: docs/Code Demos and Tutorials/LAMP Stack
---

# Tutorial 4: Frontend Integration, Droplet Deployment & End-to-End Browser Testing

In this final tutorial, you will connect the client-side user interface (HTML5, Bootstrap 5.3, and Vanilla JavaScript) to the backend RESTful API, deploy all static frontend assets to your DigitalOcean Droplet, and conduct complete end-to-end user experience testing in your web browser.

---

> [!IMPORTANT]
> **Domain Name Placeholder:**  
> Throughout this tutorial series, we use **`lamp.johnaedo.com`** as the example domain.  
> **You must replace `lamp.johnaedo.com` with the domain name you acquired from your domain registrar.**

---
## Download frontend-files.zip

You'll want to keep a copy of the front-end HTML, CSS, and Javascript files locally on your computer on your PC for ease of editing.  You'll later upload the files to your Droplet.

> [!FILES DOWNLOAD]
> [frontend-files.zip](https://teaching.johnaedo.com/code/cop4331c/lamp/frontend-files.zip)

Once you've downloaded the file, go ahead and decompress the zip file so you can inspect and edit the files.

---
## 🎨 Frontend Architecture Overview

The presentation layer is built as a decoupled, responsive client-side interface:

`/var/www/html` is the directory on the server from which your application's front-end is served.

```
 /var/www/html/
 ├── index.html        # Login Page: Form inputs, client validation, authentication trigger
 ├── color.html        # Color Manager Dashboard: Search bar, color pill badges, add form, logout
 ├── favicon.ico       # Application browser tab favicon
 ├── css/
 │   └── styles.css    # Dark mode theme, glassmorphism cards, WCAG-compliant high-contrast tokens
 ├── js/
 │   ├── code.js       # Client application logic: API calls (AJAX/XHR), cookies, DOM rendering
 │   └── md5.js        # MD5 hashing utility script
 └── images/
     ├── background.png
     └── favicon.svg
```

---

## 🧩 Step 1: Reviewing and Configuring `js/code.js`

Open [`js/code.js`](../js/code.js) in your local editor. Let's examine how the frontend communicates with the API.

### A. API Base URL Configuration
At the top of `js/code.js`, the API endpoint is defined:

```javascript
// js/code.js (Lines 1-5)
const urlBase = (typeof window !== 'undefined' && window.location && 
  (window.location.hostname === 'localhost' || 
   window.location.hostname === '127.0.0.1' || 
   window.location.origin.includes('johnaedo')))
  ? '/api/index.php'
  : 'http://lamp.johnaedo.com/api/index.php';

const loginUrlBase = urlBase;
```

This code figures out which API base URL to use depending on where the app is currently running, then reuses that same value for login requests.

**Breaking it down:**

1. **`typeof window !== 'undefined'`** — checks that this code is running in a browser environment (not on a server, like during server-side rendering in Node.js, where `window` doesn't exist). This prevents a crash if the code runs somewhere without a `window` object.
2. **`window.location &&`** — an extra safety check to make sure `window.location` actually exists before trying to read properties off it.
3. **The three OR conditions** check if the current page is being viewed from:
    - `localhost` (local development)
    - `127.0.0.1` (also local development, via IP)
    - anywhere with `johnaedo` in the origin (e.g. `johnaedo.com`, `www.johnaedo.com`, `staging.johnaedo.com`, etc. — production or related domains)
4. **The ternary (`? :`)** decides the actual URL base:
    - If any of those conditions are true → use a **relative path**: `/api/index.php`. This works because in those cases the frontend and the API are assumed to be served from the same host, so a relative URL will correctly resolve against whatever domain/port the page is currently on.
    - Otherwise (e.g. running from some other domain, or no `window` object at all) → fall back to a **hardcoded absolute URL**: `http://lamp.johnaedo.com/api/index.php`.

You'll need to update the `includes` argument to some string that uniquely distinguishes your domain name (or it can be the full domain name itself).

---

### B. Understanding Core JavaScript Functions

1. **Authentication (`doLogin()`)**:
   - Collects the username and password from the input fields in `index.html`.
   - Sends an asynchronous HTTP `POST` request with JSON payload: `{"login": login, "password": password}`.
   - If authentication succeeds (`status === 200` and `userId > 0`), it invokes `saveCookie()` and redirects the browser to `color.html`.
   - If authentication fails, it displays an alert message inside `#loginResult`.

2. **Session Persistence (`saveCookie()` and `readCookie()`)**:
   - `saveCookie()`: Serializes `firstName`, `lastName`, and `userId` into a client-side HTTP cookie with a 20-minute expiration time.
   - `readCookie()`: Executed immediately when `color.html` loads (`DOMContentLoaded`). If no valid cookie exists, it automatically redirects the user back to `index.html` (protecting the dashboard). If valid, it renders the user's name and calls `searchColor()`.

3. **Search & Dynamic Rendering (`searchColor()`)**:
   - Queries the API via `GET /api/index.php?q=<search>` with header `Authorization: Bearer <userId>`.
   - Receives JSON array of colors.
   - Dynamically constructs HTML badge pills with color swatch indicators and delete buttons:
     ```javascript
     colorList += `<span class="badge rounded-pill bg-dark-subtle text-body border border-secondary px-3 py-2 fs-6 shadow-sm d-inline-flex align-items-center me-2 mb-2">
       <span class="d-inline-block rounded-circle me-2 border" style="width: 14px; height: 14px; background-color: ${colorName};"></span>
       <span class="me-2">${colorName}</span>
       <button type="button" class="btn-close btn-close-white" style="font-size: 0.65rem;" onclick="deleteColor(${colorId ? colorId : `'${colorName}'`});" title="Delete Color"></button>
     </span>`;
     ```

4. **Add Color (`addColor()`)**:
   - Reads `#colorText`, validates non-empty input.
   - Sends `POST /api/index.php` with `{"color": newColor}` and `Authorization: Bearer <userId>`.
   - On success (`201 Created`), clears input field and refreshes the color list.

5. **Delete Color (`deleteColor()`)**:
   - Sends `DELETE /api/index.php?id=<id>` with `Authorization: Bearer <userId>`.
   - On completion, invokes `searchColor()` to re-render the palette.

6. **Log Out (`doLogout()`)**:
   - Sets cookie expiration dates to the past (clearing cookies) and redirects to `index.html`.

---

## 🚀 Step 2: Deploy Frontend Files to the Droplet

Now copy all frontend assets (`index.html`, `color.html`, `css/`, `js/`, `images/`, `favicon.ico`) to `/var/www/html/` on your Droplet.

### From Local Machine using `scp`

```bash
scp -r index.html color.html favicon.ico css js images root@<YOUR_DROPLET_IP>:/var/www/html/
```

---

## 🔒 Step 3: Verify Web Server Directory Structure & Permissions

SSH into your Droplet:
```bash
ssh root@<YOUR_DROPLET_IP>
```

1. Check that all files are present in `/var/www/html`:
   ```bash
   ls -la /var/www/html
   ```
   *Expected listing:*
   ```text
   drwxr-xr-x 6 www-data www-data  4096 Aug 24 14:00 .
   -rw------- 1 www-data www-data   120 Aug 24 14:00 .env
   drwxr-xr-x 3 www-data www-data  4096 Aug 24 14:00 api
   -rw-r--r-- 1 www-data www-data  5505 Aug 24 14:00 color.html
   drwxr-xr-x 2 www-data www-data  4096 Aug 24 14:00 css
   -rw-r--r-- 1 www-data www-data  1150 Aug 24 14:00 favicon.ico
   drwxr-xr-x 2 www-data www-data  4096 Aug 24 14:00 images
   -rw-r--r-- 1 www-data www-data  4455 Aug 24 14:00 index.html
   drwxr-xr-x 2 www-data www-data  4096 Aug 24 14:00 js
   ```

2. Ensure correct ownership and permissions:
   ```bash
   sudo chown -R www-data:www-data /var/www/html
   sudo find /var/www/html -type d -exec chmod 755 {} \;
   sudo find /var/www/html -type f -exec chmod 644 {} \;
   sudo chmod 600 /var/www/html/.env
   ```

---

## 🌐 Step 4: End-to-End Browser Testing Walkthrough

Open your web browser (Chrome, Firefox, Safari, or Edge) and navigate to:
```
http://lamp.johnaedo.com
```
*(Replace `lamp.johnaedo.com` with your registered domain).*

---

### Test Flow 1: Login & Form Validation
1. **Empty Form Submission**:
   - Leave fields blank and click **Sign In**.
   - Notice browser HTML5 validation prompts for required inputs.
2. **Invalid Credentials**:
   - Username: `RickL`
   - Password: `WRONG_PASSWORD`
   - Click **Sign In**.
   - Verify error feedback appears:  
     `<i class='bi bi-exclamation-circle-fill'></i> User/Password combination incorrect`
3. **Valid Login**:
   - Username: `RickL`
   - Password: `COP4331`
   - Click **Sign In** (or press Enter).
   - Verify immediate transition to `color.html`.

---

### Test Flow 2: Session & Cookie Verification
1. On `color.html`, press **`F12`** (or right-click and choose **Inspect**) to open Browser Developer Tools.
2. Navigate to the **Application** tab (Chrome/Edge) or **Storage** tab (Firefox).
3. Expand **Cookies** and select `http://lamp.johnaedo.com`.
4. Verify the following three cookies are stored:
   - `userId`: `1`
   - `firstName`: `Rick`
   - `lastName`: `Leinecker`
5. Verify the header bar on the page displays:  
   **"Logged in as Rick Leinecker"**.

---

### Test Flow 3: Palette Display & Live Search
1. When `color.html` loads, observe that default palette badges (Blue, White, Black, Magenta, Yellow, Cyan, Salmon, etc.) are rendered with colored preview dots.
2. **Search for Colors**:
   - In the *Search Colors* field, type **`Blue`** and click **Search Color** (or press Enter).
   - Verify the list filters to show only **Blue** and **Light Blue**.
   - Notice the status message: `Results updated`.
3. **Search for Non-Existent Color**:
   - Type **`Purple`** and search.
   - Verify message: `No matching colors found.`
4. **Clear Search**:
   - Clear the search box and click **Search Color**.
   - Verify all 17 default colors re-appear.

---

### Test Flow 4: Adding New Colors
1. Scroll to the **Add New Color** card.
2. Enter **`Chartreuse`** (or a color hex like `#4A90E2`).
3. Click **Add Color** (or press Enter).
4. Verify:
   - Success message: `<i class='bi bi-check-circle-fill'></i> Color successfully added!`
   - The input field resets.
   - The new color pill badge immediately appears in the palette above.

---

### Test Flow 5: Deleting Colors
1. Locate any color badge (for example, the newly created color).
2. Click the small white **`×`** close button inside the badge.
3. The badge is deleted from the MySQL database and vanishes from the screen.

---

### Test Flow 6: Protected Routes & Log Out
1. Click the red **Log Out** button in the top header.
2. Verify you are redirected back to `index.html`.
3. Check DevTools Cookies to confirm session cookies have been cleared.
4. **Direct URL Access Test**:
   - While logged out, manually enter `http://lamp.johnaedo.com/color.html` into your browser address bar and press Enter.
   - Verify `readCookie()` immediately detects missing credentials and redirects you back to `index.html`.

---

## 🛠️ Step 5: Troubleshooting & Developer Tips

| Issue | Cause | Solution |
| :--- | :--- | :--- |
| **Page changes not showing up** | Browser caching old `code.js` or `styles.css` | Perform a hard refresh: `Ctrl + F5` (Windows) or `Cmd + Shift + R` (macOS). |
| **403 Forbidden Error** | Incorrect Linux file permissions on `/var/www/html` | Run `sudo chown -R www-data:www-data /var/www/html` and `sudo chmod -R 755 /var/www/html`. |
| **Login fails with red error message** | API cannot connect to MySQL | Check `/var/www/html/.env` credentials and verify `systemctl status mysql`. |
| **CORS / Network Error in Console** | Mixed HTTP/HTTPS or wrong `urlBase` in `code.js` | Ensure `code.js` uses relative `'/api/index.php'` or matches current protocol (`http://` vs `https://`). |

---

## 🔒 Optional Bonus: Enable Free HTTPS with Let's Encrypt (Certbot)

To secure your production Droplet with an SSL/TLS certificate:

1. SSH into your Droplet:
   ```bash
   ssh root@<YOUR_DROPLET_IP>
   ```
2. Install Certbot:
   ```bash
   sudo apt-get install -y certbot python3-certbot-apache
   ```
3. Request and install certificate for your domain:
   ```bash
   sudo certbot --apache -d lamp.johnaedo.com
   ```
   *(Enter your email address and accept terms when prompted).*
4. Certbot will automatically reconfigure Apache to redirect all HTTP traffic to secure **`https://lamp.johnaedo.com`**!

---

## 🏆 Project Complete!

Congratulations! You have successfully built, configured, deployed, and tested a modern, decoupled full-stack LAMP web application:
1. **Infrastructure**: Ubuntu Linux Droplet on DigitalOcean with SSH key authentication and custom DNS routing.
2. **Database Tier**: MySQL relational persistence with `Users` and `Colors` tables and least-privilege security.
3. **Backend API Tier**: PHP RESTful controller communicating over JSON, verified with Bruno integration tests.
4. **Presentation Tier**: Responsive Bootstrap 5.3 dark interface with client-side cookie session management and asynchronous CRUD operations.
