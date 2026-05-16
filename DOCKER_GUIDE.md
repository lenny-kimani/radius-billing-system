# PHPNuxBill Alpine Docker Guide

This guide details how to build, run, and manage the optimized Alpine-based environment for PHPNuxBill.

## 🚀 Quick Start (Automated)

The current setup is designed to move from **build to admin panel** with zero manual configuration files.

```bash
# 1. Start the entire stack
podman-compose -f docker-compose-alpine.yml up -d

# 2. Access the installer to initialize the database schema
# Open: http://localhost:8080/install
```

## 🛠 Manual Inputs Required

While the container automates the `config.php` and directory setup, the following steps still require user interaction in the web browser:

1.  **Database Initialization**: On first run, you **must** visit `http://localhost:8080/install`.
    *   **Database Host**: `db`
    *   **Database User**: `phpnuxbill` (default in .env)
    *   **Database Pass**: `password` (default in .env)
    *   **Database Name**: `phpnuxbill`
2.  **Admin Credentials**: After installation, log in at `http://localhost:8080/admin`.
    *   **Username**: `admin`
    *   **Password**: `admin`

## 📋 Important Details

### Persistence
The following directories are marked as `VOLUMES` in the Dockerfile to ensure your data survives container updates:
*   `/var/www/html/system/uploads`: User documents and system files.
*   `/var/www/html/system/cache`: System cache.
*   `/var/www/html/ui/cache`: UI performance cache.
*   `/var/www/html/ui/compiled`: Compiled templates.

### Automated Services
The container uses **Supervisor** to manage multiple services:
*   **Nginx**: Web server.
*   **PHP-FPM**: PHP processor.
*   **Crond**: Handles automated expiration and reminders every minute.

### Environment Variables
You can customize the connection by editing the `.env` file or the `environment` section in `docker-compose-alpine.yml`:
*   `DB_HOST`: Address of the MySQL container (default: `db`).
*   `DB_USER`: Database username.
*   `DB_PASS`: Database password.
*   `DB_NAME`: Database name.

### Resource Usage
This Alpine build is highly efficient, typically consuming:
*   **RAM**: ~26 MB
*   **CPU**: <1% (Idle)

---
**Security Note**: After a successful installation, it is recommended to remove or restrict access to the `/var/www/html/install` directory.
