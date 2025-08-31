# Day 20: Configure Nginx + PHP-FPM Using Unix Socket

## 🎯 Task

The goal is to configure Nginx to work with PHP-FPM using a Unix socket at the specified path:

```
/var/run/php-fpm/default.sock
```

> **Note:** If the directory path does not exist, create it manually.

---

## ⚠️ Problems Faced

### 1. PHP Version Issue

- Installing the recommended PHP version from base repositories resulted in Nginx displaying:
  ```
  404 Page Not Found
  ```

- Installing `php-fpm` without version suffix caused the server to display:
  ```
  Welcome to xFusionCorp
  ```

- In both cases, the assessment failed with errors:
  - Wrong PHP-FPM version installed
  - Or no PHP-FPM installed at all

### 2. PHP-FPM Socket Ownership

The task required PHP-FPM to listen via the socket:
```
/var/run/php-fpm/default.sock
```

By default, PHP-FPM's pool file (`/etc/php-fpm.d/www.conf`) runs under:
```
user = apache
group = apache
```

The parent directory and socket need proper ownership:
```bash
sudo chown -R apache:apache /var/run/php-fpm
```

### 3. Repo / Package Source Confusion

Default repositories only offered older PHP versions, causing version mismatches with the assessment's expectations.

---

## ✅ Solution

### Enable Remi Repository

The Remi repository provides the PHP version expected by KodeKloud assessments.

```bash
sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-8.rpm -y
sudo dnf module reset php -y
sudo dnf module enable php:remi-8.0 -y
sudo dnf install php php-fpm -y
```

### Configure PHP-FPM Socket

Edit `/etc/php-fpm.d/www.conf` to set the socket and user/group:

```ini
listen = /var/run/php-fpm/default.sock
user = apache
group = apache
```

Create the socket directory if it does not exist:

```bash
sudo mkdir -p /var/run/php-fpm
sudo chown -R apache:apache /var/run/php-fpm
```

### Configure Nginx

Update the Nginx configuration (e.g., `/etc/nginx/conf.d/default.conf`) to handle PHP requests:

```nginx
location ~ \.php$ {
    root   /usr/share/nginx/html;
    fastcgi_pass   unix:/var/run/php-fpm/default.sock;
    fastcgi_index  index.php;
    include        fastcgi_params;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
}
```

### Restart Services

Restart PHP-FPM and Nginx to apply changes:

```bash
sudo systemctl restart php-fpm
sudo systemctl restart nginx
```

---

## 🚀 Final Result

- ✅ Nginx and PHP-FPM worked correctly using the Unix socket
- ✅ PHP files executed successfully
- ✅ The assessment passed only after installing the correct PHP version from the Remi repository

---

## 📝 Key Takeaways

1. **Always verify the PHP version required by the assessment**
2. **Default repository PHP versions may not match the requirements**
3. **The Remi repository is often necessary for CentOS/RHEL-based labs**
4. **Ensure socket ownership matches the user/group defined in `www.conf` (e.g., `apache`)**

---

## 🔍 Verification

If all steps return the expected results, the configuration is correct and ready for assessment submission.

### Test PHP Processing

Create a test PHP file:

```bash
echo "<?php phpinfo(); ?>" | sudo tee /usr/share/nginx/html/info.php
```

Access the file via browser to verify PHP is processing correctly.

### Check Services Status

```bash
sudo systemctl status php-fpm
sudo systemctl status nginx
```

Both services should be active and running without error