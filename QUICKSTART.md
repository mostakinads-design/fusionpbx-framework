# FusionPBX Quick Start Guide

## Quick Installation with MySQL

This guide will help you get FusionPBX up and running with MySQL in the shortest time possible.

## Prerequisites Check

Before starting, ensure you have:
- A Linux server (Ubuntu 20.04+ or Debian 11+ recommended)
- Root or sudo access
- At least 2GB RAM
- 10GB free disk space

## Step 1: Install Required Packages

```bash
# Update system
sudo apt-get update && sudo apt-get upgrade -y

# Install MySQL
sudo apt-get install -y mysql-server mysql-client

# Install Apache and PHP
sudo apt-get install -y apache2 php php-cli php-fpm \
    php-mysql php-pdo php-pgsql php-sqlite3 \
    php-curl php-xml php-gd php-mbstring php-imap

# Install FreeSWITCH (optional but recommended for full PBX functionality)
# Follow official FreeSWITCH installation guide for your OS
```

## Step 2: Secure MySQL

```bash
sudo mysql_secure_installation
```

Answer the prompts:
- Set root password: Yes
- Remove anonymous users: Yes
- Disallow root login remotely: Yes
- Remove test database: Yes
- Reload privilege tables: Yes

## Step 3: Create Database

```bash
sudo mysql -u root -p
```

Then run these SQL commands:

```sql
CREATE DATABASE fusionpbx CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'fusionpbx'@'localhost' IDENTIFIED BY 'YourSecurePassword123!';
GRANT ALL PRIVILEGES ON fusionpbx.* TO 'fusionpbx'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

**Important**: Replace `YourSecurePassword123!` with a strong password!

## Step 4: Install FusionPBX

```bash
# Navigate to web root
cd /var/www

# Clone repository
sudo git clone https://github.com/mostakinads-design/fusionpbx-framework.git fusionpbx

# Set permissions
sudo chown -R www-data:www-data /var/www/fusionpbx
sudo chmod -R 755 /var/www/fusionpbx

# Create config directory
sudo mkdir -p /etc/fusionpbx
sudo chown www-data:www-data /etc/fusionpbx
```

## Step 5: Configure Apache

Create Apache virtual host:

```bash
sudo nano /etc/apache2/sites-available/fusionpbx.conf
```

Add this configuration:

```apache
<VirtualHost *:80>
    ServerAdmin admin@example.com
    ServerName your-domain.com
    DocumentRoot /var/www/fusionpbx
    
    <Directory /var/www/fusionpbx>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/fusionpbx_error.log
    CustomLog ${APACHE_LOG_DIR}/fusionpbx_access.log combined
</VirtualHost>
```

Enable the site and rewrite module:

```bash
sudo a2enmod rewrite
sudo a2ensite fusionpbx.conf
sudo a2dissite 000-default.conf
sudo systemctl restart apache2
```

## Step 6: Run Web Installer

1. Open your web browser and navigate to:
   ```
   http://your-server-ip/core/install/install.php
   ```

2. **Step 1 - Account Setup**:
   - Admin Username: `admin`
   - Admin Password: Choose a strong password
   - Domain Name: Your server's hostname or IP

3. **Step 2 - Database Setup**:
   - **Database Type**: Select **MySQL**
   - Host: `localhost`
   - Port: `3306` (auto-filled)
   - Database Name: `fusionpbx`
   - Username: `fusionpbx`
   - Password: The password you set in Step 3

4. Click **Install** and wait for completion

## Step 7: First Login

1. Navigate to: `http://your-server-ip/`
2. Login with the admin credentials you created
3. You should see the FusionPBX dashboard

## Step 8: Post-Installation Configuration

### Secure Your Installation

1. **Enable HTTPS** (Strongly Recommended):
   ```bash
   sudo apt-get install certbot python3-certbot-apache
   sudo certbot --apache -d your-domain.com
   ```

2. **Configure Firewall**:
   ```bash
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   sudo ufw allow 5060:5091/tcp  # SIP/FreeSWITCH
   sudo ufw allow 16384:32768/udp  # RTP media
   sudo ufw enable
   ```

### Basic PBX Setup

1. **Create Extensions**:
   - Go to: Accounts → Extensions
   - Click Add
   - Fill in extension number and password
   - Save

2. **Create a Ring Group** (Optional):
   - Go to: Dialplan → Ring Groups
   - Click Add
   - Configure extension numbers to ring

3. **Set Up Voicemail**:
   - Go to: Apps → Voicemail
   - Configure voicemail settings per extension

## Verification

Test that everything is working:

1. **Check Database Connection**:
   - Log into FusionPBX
   - Go to: Status → System
   - Verify database type shows "mysql"

2. **Check FreeSWITCH** (if installed):
   - Go to: Status → FreeSWITCH Status
   - Should show "Connected"

3. **Register an Extension**:
   - Configure a SIP phone with extension credentials
   - Verify registration in Status → Registrations

## Troubleshooting

### Can't Access Web Interface

```bash
# Check Apache is running
sudo systemctl status apache2

# Check Apache error logs
sudo tail -f /var/log/apache2/fusionpbx_error.log
```

### Database Connection Errors

```bash
# Test database connection
mysql -u fusionpbx -p fusionpbx

# Check config file
sudo cat /etc/fusionpbx/config.conf
```

### Permission Errors

```bash
# Reset permissions
sudo chown -R www-data:www-data /var/www/fusionpbx
sudo chmod -R 755 /var/www/fusionpbx
sudo chown -R www-data:www-data /etc/fusionpbx
```

## Next Steps

1. **Configure External SIP Profile** for external calls
2. **Set Up Outbound Routes** for PSTN calling
3. **Configure IVR** for auto-attendant
4. **Enable Call Recording** if needed
5. **Set Up Backups** (database + config)

## Important Security Notes

- Always use HTTPS in production
- Change default passwords immediately
- Restrict administrative access with firewall rules
- Keep all software updated
- Enable fail2ban for brute-force protection
- Regular database backups
- Monitor logs for suspicious activity

## Getting Help

- Full Documentation: See README.md
- MySQL Guide: See MYSQL_SETUP.md
- FusionPBX Community: https://www.fusionpbx.com/
- FreeSWITCH Docs: https://freeswitch.org/confluence/

## Quick Reference Commands

```bash
# Restart Apache
sudo systemctl restart apache2

# Restart MySQL
sudo systemctl restart mysql

# Check MySQL status
sudo systemctl status mysql

# View FusionPBX logs
sudo tail -f /var/log/apache2/fusionpbx_error.log

# Backup database
mysqldump -u fusionpbx -p fusionpbx > backup_$(date +%Y%m%d).sql

# Check PHP version
php -v

# Check installed PHP modules
php -m
```

## Congratulations!

You now have a working FusionPBX installation with MySQL! 🎉

For advanced configuration and features, refer to the main README.md and official FusionPBX documentation.
