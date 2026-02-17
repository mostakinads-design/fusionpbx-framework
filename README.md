# FusionPBX Framework

## Overview

FusionPBX is an open-source web-based PBX (Private Branch Exchange) management interface for FreeSWITCH. This framework provides a comprehensive web interface for managing phone systems with features like call routing, voicemail, conferencing, IVR, and more.

## Features

- **Multi-tenant**: Support for multiple domains/tenants
- **Web-based Interface**: Easy-to-use web GUI for administration
- **Call Management**: Advanced call routing, call flows, and dialplans
- **Voicemail**: Full-featured voicemail with email notifications
- **Conference Bridge**: Audio and video conferencing capabilities
- **IVR (Interactive Voice Response)**: Create custom call menus
- **Extensions**: Manage SIP extensions and devices
- **Ring Groups**: Group extensions for simultaneous or sequential ringing
- **Call Center**: Queue management and agent functionality
- **Time Conditions**: Route calls based on time and date
- **Recording**: Call recording and monitoring
- **Fax Server**: Send and receive faxes
- **User Management**: Fine-grained permission system
- **API**: RESTful API for integration

## Database Support

The framework supports multiple database backends:

- **PostgreSQL** (default, recommended for production)
- **MySQL/MariaDB** (fully supported)
- **SQLite** (for FreeSWITCH switch database)

## System Requirements

### Minimum Requirements

- **Operating System**: Linux (Debian, Ubuntu, CentOS, RHEL), FreeBSD, or Windows
- **PHP**: 7.4 or higher (8.0+ recommended)
- **Web Server**: Apache 2.4+ or Nginx 1.18+
- **Database**: PostgreSQL 10+ or MySQL 5.7+ (8.0+ recommended)
- **FreeSWITCH**: 1.10.x (latest stable version)
- **RAM**: 2GB minimum (4GB+ recommended)
- **Disk Space**: 10GB minimum (more for recordings and voicemail)

### PHP Extensions Required

- pdo
- pdo_pgsql (for PostgreSQL) or pdo_mysql (for MySQL)
- pdo_sqlite
- xml
- curl
- json
- openssl
- imap (optional, for voicemail to email)
- gd (for image manipulation)
- mbstring

## Installation

### Quick Start

1. **Install Prerequisites**:

```bash
# Debian/Ubuntu
sudo apt-get update
sudo apt-get install apache2 php php-cli php-pdo php-pgsql php-sqlite3 \
     php-curl php-xml php-gd php-mbstring postgresql

# CentOS/RHEL
sudo yum install httpd php php-cli php-pdo php-pgsql php-sqlite3 \
     php-curl php-xml php-gd php-mbstring postgresql-server
```

2. **Install FreeSWITCH**:

Follow the official FreeSWITCH installation guide for your operating system:
https://freeswitch.org/confluence/display/FREESWITCH/Installation

3. **Clone FusionPBX Framework**:

```bash
cd /var/www
sudo git clone https://github.com/mostakinads-design/fusionpbx-framework.git fusionpbx
sudo chown -R www-data:www-data /var/www/fusionpbx
```

4. **Configure Web Server**:

Create an Apache virtual host or Nginx server block pointing to the FusionPBX directory.

Example Apache configuration:

```apache
<VirtualHost *:80>
    ServerName fusionpbx.example.com
    DocumentRoot /var/www/fusionpbx
    
    <Directory /var/www/fusionpbx>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/fusionpbx_error.log
    CustomLog ${APACHE_LOG_DIR}/fusionpbx_access.log combined
</VirtualHost>
```

5. **Set Up Database**:

**For PostgreSQL**:
```bash
sudo -u postgres psql
CREATE DATABASE fusionpbx;
CREATE USER fusionpbx WITH PASSWORD 'your_password';
GRANT ALL PRIVILEGES ON DATABASE fusionpbx TO fusionpbx;
\q
```

**For MySQL**:
```bash
sudo mysql -u root -p
CREATE DATABASE fusionpbx CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'fusionpbx'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON fusionpbx.* TO 'fusionpbx'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

See [MYSQL_SETUP.md](MYSQL_SETUP.md) for detailed MySQL setup instructions.

6. **Run Web Installer**:

Navigate to `http://your-server-ip/core/install/install.php` and follow the installation wizard.

### Configuration Directories

The installer will create configuration files in:

- **Linux**: `/etc/fusionpbx/config.conf`
- **BSD**: `/usr/local/etc/fusionpbx/config.conf`
- **Windows**: `C:\ProgramData\fusionpbx\config.conf`

## Directory Structure

```
fusionpbx-framework/
├── app/                    # Application modules (deprecated, use core/)
├── core/                   # Core modules
│   ├── authentication/     # User authentication
│   ├── dashboard/          # Main dashboard
│   ├── databases/          # Database management
│   ├── domains/            # Multi-tenant domain management
│   ├── install/            # Installation wizard
│   ├── menu/               # Navigation menu
│   ├── permissions/        # Permission system
│   ├── users/              # User management
│   └── ...
├── resources/              # Shared resources
│   ├── classes/            # PHP classes (database, config, etc.)
│   ├── functions/          # Utility functions
│   ├── templates/          # Template engine
│   └── ...
├── themes/                 # UI themes
├── index.php               # Main entry point
├── login.php               # Login page
└── logout.php              # Logout handler
```

## Usage

### Default Login

After installation, log in with the credentials you created during setup:

- **URL**: `http://your-server-ip/`
- **Username**: admin (or what you specified)
- **Password**: (what you specified during installation)

### Adding Extensions

1. Navigate to **Accounts** > **Extensions**
2. Click **Add** button
3. Fill in extension details (number, password, etc.)
4. Configure voicemail and other settings
5. Save the extension

### Creating Dialplans

1. Navigate to **Dialplan** > **Dialplan Manager**
2. Click **Add** to create a new dialplan
3. Define conditions and actions
4. Apply changes to FreeSWITCH

### Managing Domains

1. Navigate to **Advanced** > **Domains**
2. Add new domains for multi-tenant setup
3. Configure domain-specific settings

## API Access

FusionPBX provides a RESTful API for integration with external systems. API documentation can be accessed from the web interface under **Advanced** > **API**.

## Security Considerations

1. **Change Default Passwords**: Always change default passwords immediately after installation
2. **Use HTTPS**: Configure SSL/TLS certificates for secure access
3. **Firewall**: Restrict access to administrative interfaces
4. **Regular Updates**: Keep the system and all components up to date
5. **Database Security**: Use strong passwords and restrict database access
6. **File Permissions**: Ensure proper file permissions (755 for directories, 644 for files)
7. **Fail2Ban**: Consider using Fail2Ban to prevent brute-force attacks

## Backup and Restore

### Backup

```bash
# Backup database (PostgreSQL)
sudo -u postgres pg_dump fusionpbx > fusionpbx_backup_$(date +%Y%m%d).sql

# Backup database (MySQL)
mysqldump -u fusionpbx -p fusionpbx > fusionpbx_backup_$(date +%Y%m%d).sql

# Backup configuration
sudo tar -czf fusionpbx_config_$(date +%Y%m%d).tar.gz /etc/fusionpbx

# Backup recordings and voicemail
sudo tar -czf fusionpbx_data_$(date +%Y%m%d).tar.gz /var/lib/freeswitch
```

### Restore

```bash
# Restore database (PostgreSQL)
sudo -u postgres psql fusionpbx < fusionpbx_backup_20240101.sql

# Restore database (MySQL)
mysql -u fusionpbx -p fusionpbx < fusionpbx_backup_20240101.sql

# Restore configuration
sudo tar -xzf fusionpbx_config_20240101.tar.gz -C /

# Restore recordings and voicemail
sudo tar -xzf fusionpbx_data_20240101.tar.gz -C /
```

## Troubleshooting

### Common Issues

1. **Cannot Connect to Database**
   - Verify database credentials in config.conf
   - Check database service is running
   - Verify PHP PDO extension is installed

2. **FreeSWITCH Not Connecting**
   - Check FreeSWITCH is running: `systemctl status freeswitch`
   - Verify FreeSWITCH XML configuration
   - Check event socket settings

3. **Permission Denied Errors**
   - Check file ownership: `chown -R www-data:www-data /var/www/fusionpbx`
   - Check directory permissions
   - Verify cache directory is writable

4. **White Screen/PHP Errors**
   - Check PHP error logs
   - Verify all required PHP extensions are installed
   - Check PHP version compatibility

## Development

### Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

### Code Standards

- Follow PSR-12 coding standards for PHP
- Use tabs for indentation
- Document functions and classes
- Write meaningful commit messages

## License

FusionPBX is licensed under the Mozilla Public License (MPL) Version 1.1.

## Support

- **Documentation**: https://docs.fusionpbx.com/
- **Community**: https://www.fusionpbx.com/
- **GitHub Issues**: Report bugs and request features

## Acknowledgments

- **Mark J. Crane**: Original developer and maintainer
- **FreeSWITCH Team**: For the excellent telephony platform
- **Community Contributors**: For continuous improvements

## Additional Resources

- [MySQL Setup Guide](MYSQL_SETUP.md)
- [FreeSWITCH Documentation](https://freeswitch.org/confluence/)
- [SIP Protocol Reference](https://datatracker.ietf.org/doc/html/rfc3261)
- [VoIP Security Best Practices](https://www.voipsecurity.org/)
