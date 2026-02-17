# FusionPBX Framework - MySQL Setup Guide

## Overview

FusionPBX Framework now supports MySQL as an alternative to PostgreSQL for the system database. This guide will help you set up FusionPBX with MySQL.

## Prerequisites

- MySQL Server 5.7 or higher (MySQL 8.0 recommended)
- PHP with PDO_MySQL extension enabled
- Web server (Apache or Nginx)
- FreeSWITCH (for PBX functionality)

## MySQL Installation

### On Debian/Ubuntu:

```bash
sudo apt-get update
sudo apt-get install mysql-server mysql-client
sudo mysql_secure_installation
```

### On CentOS/RHEL:

```bash
sudo yum install mysql-server mysql
sudo systemctl start mysqld
sudo mysql_secure_installation
```

## Database Setup

1. Log into MySQL as root:

```bash
sudo mysql -u root -p
```

2. Create the FusionPBX database and user:

```sql
CREATE DATABASE fusionpbx CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'fusionpbx'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON fusionpbx.* TO 'fusionpbx'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

## FusionPBX Installation with MySQL

1. Navigate to your FusionPBX installation in a web browser:

```
http://your-server-ip/core/install/install.php
```

2. On **Step 1** (Account Configuration):
   - Enter the admin username (default: admin)
   - Enter a secure admin password
   - Enter your domain name

3. On **Step 2** (Database Configuration):
   - **Database Type**: Select **MySQL** from the dropdown
   - **Host**: localhost (or your MySQL server IP)
   - **Port**: 3306 (default MySQL port, will auto-fill when you select MySQL)
   - **Database Name**: fusionpbx
   - **Username**: fusionpbx
   - **Password**: your_secure_password (the one you set above)

4. Click **Install** to complete the installation.

## Configuration File

The installation creates a configuration file at:

- **Linux**: `/etc/fusionpbx/config.conf`
- **BSD**: `/usr/local/etc/fusionpbx/config.conf`
- **Windows**: `C:\ProgramData\fusionpbx\config.conf`

### Example MySQL Configuration:

```ini
#database system settings
database.0.type = mysql
database.0.host = localhost
database.0.port = 3306
database.0.name = fusionpbx
database.0.username = fusionpbx
database.0.password = your_secure_password

#database switch settings
database.1.type = sqlite
database.1.path = /var/lib/freeswitch/db
database.1.name = core.db
```

## Verifying MySQL Connection

After installation, verify the database connection by checking:

1. Log into FusionPBX with your admin credentials
2. Navigate to Status > System Information
3. Verify that the database type shows "mysql"

## Performance Tuning for MySQL

For better performance with FusionPBX, consider these MySQL optimizations:

### Edit `/etc/mysql/mysql.conf.d/mysqld.cnf` (or your MySQL config file):

```ini
[mysqld]
# Increase connection limit
max_connections = 200

# Buffer pool size (set to 70-80% of available RAM for dedicated DB server)
innodb_buffer_pool_size = 1G

# Log file size
innodb_log_file_size = 256M

# Character set
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

# SQL mode compatibility
sql_mode = "STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION"
```

Restart MySQL after making changes:

```bash
sudo systemctl restart mysql
```

## Troubleshooting

### Connection Issues

If you encounter connection issues:

1. Verify MySQL is running:
   ```bash
   sudo systemctl status mysql
   ```

2. Test database connection:
   ```bash
   mysql -u fusionpbx -p fusionpbx
   ```

3. Check PHP PDO MySQL extension:
   ```bash
   php -m | grep pdo_mysql
   ```

4. Verify MySQL user permissions:
   ```sql
   SHOW GRANTS FOR 'fusionpbx'@'localhost';
   ```

### Port Already in Use

If port 3306 is already in use, you can configure MySQL to use a different port:

1. Edit MySQL configuration file
2. Change the port setting
3. Update the FusionPBX configuration accordingly

## Migrating from PostgreSQL to MySQL

If you need to migrate an existing PostgreSQL installation to MySQL:

1. Export your PostgreSQL data using `pg_dump`
2. Convert the SQL dump to MySQL-compatible format
3. Import into MySQL
4. Update the configuration file to use MySQL settings
5. Test thoroughly before going to production

**Note**: Direct migration may require schema adjustments due to differences between PostgreSQL and MySQL.

## Support

For issues or questions:
- Check the FusionPBX documentation
- Visit the FusionPBX community forums
- Review MySQL documentation for database-specific issues

## Security Recommendations

1. Use strong passwords for database users
2. Restrict database access to localhost when possible
3. Keep MySQL updated with security patches
4. Enable MySQL SSL/TLS for remote connections
5. Regularly backup your database
6. Use firewalls to restrict database port access

## Backup and Restore

### Backup:
```bash
mysqldump -u fusionpbx -p fusionpbx > fusionpbx_backup_$(date +%Y%m%d).sql
```

### Restore:
```bash
mysql -u fusionpbx -p fusionpbx < fusionpbx_backup_20240101.sql
```

## Additional Resources

- MySQL Documentation: https://dev.mysql.com/doc/
- FusionPBX Documentation: https://docs.fusionpbx.com/
- FreeSWITCH Documentation: https://freeswitch.org/confluence/
