# WordPress Development Environment

This repository contains a Docker-based development environment for WordPress 6.8 with PHP 8.2 and Nginx.

## Directory Structure

```
.
├── docker/                  # Docker configuration files
│   ├── nginx/              # Nginx server configuration
│   └── php/                # PHP Dockerfile and configuration
├── src/                    # WordPress core, themes, and plugins (mounted to /var/www/html)
└── docker-compose.yml      # Docker Compose configuration
```

## Setting Up WordPress

### 1. Place WordPress Files

1. Extract the latest WordPress installation into the `src` directory:
   ```bash
   cd /path/to/this/repository
   wget https://wordpress.org/latest.tar.gz
   tar -xzvf latest.tar.gz --strip-components=1 -C src/
   rm latest.tar.gz
   ```

2. Or clone an existing WordPress project into the `src` directory.

### 2. File Permissions

Set proper permissions for WordPress:
```bash
chown -R www-data:www-data src/
find src/ -type d -exec chmod 755 {} \;
find src/ -type f -exec chmod 644 {} \;
```

### 3. Start the Environment

```bash
docker-compose up -d --build
```

### 4. WordPress Configuration

1. Access the WordPress installation at: http://localhost:8080
2. Follow the WordPress installation wizard
3. Use the following database credentials:
   - Database Host: `db` (when using included MySQL container)
   - Database Name: `wordpress`
   - Database User: `wordpress`
   - Database Password: `wordpress`

## Development Workflow

- **Themes**: Place custom themes in `src/wp-content/themes/`
- **Plugins**: Place custom plugins in `src/wp-content/plugins/`
- **Uploads**: Files uploaded through WordPress will be stored in `src/wp-content/uploads/`

## Useful Commands

- Start services: `docker-compose up -d`
- Stop services: `docker-compose down`
- View logs: `docker-compose logs -f`
- Access PHP container: `docker-compose exec php bash`
- Access MySQL (if using included container): `docker-compose exec db mysql -uwordpress -pwordpress`

## Environment Configuration

- **PHP Version**: 8.2
- **Nginx Version**: Latest Alpine
- **MySQL Version**: 8.0 (if using included container)

## Security Notes

- Change default database credentials in `docker-compose.yml` for production
- Never commit sensitive information (like `.env` files) to version control
- Configure proper file permissions in production

## Scheduled Maintenance

### Weekly Container Restart (Ubuntu 22.04)

To ensure optimal performance and apply any system updates, you can set up a weekly restart of your Docker containers. Here's how to schedule it for Sunday at 2:00 AM:

1. Create a shell script to restart the services:
   ```bash
   sudo nano /usr/local/bin/restart-wordpress.sh
   ```

2. Add the following content (adjust the directory to your project location):
   ```bash
   #!/bin/bash
   cd /path/to/your/wordpress/project
   /usr/bin/docker-compose down
   /usr/bin/docker-compose up -d
   ```

3. Make the script executable:
   ```bash
   sudo chmod +x /usr/local/bin/restart-wordpress.sh
   ```

4. Set up a cron job to run the script weekly:
   ```bash
   sudo crontab -e
   ```

5. Add this line to run the script every Sunday at 2:00 AM:
   ```
   0 2 * * 0 /usr/local/bin/restart-wordpress.sh > /var/log/wordpress-restart.log 2>&1
   ```

6. Save and exit. The cron job will now run automatically.

## Troubleshooting

- If you encounter permission issues, run:
  ```bash
  sudo chown -R $USER:$USER src/
  ```
- If WordPress can't write to the wp-content directory, ensure the web server has write permissions:
  ```bash
  chmod -R 755 src/wp-content/
  chmod -R 775 src/wp-content/uploads/
  ```