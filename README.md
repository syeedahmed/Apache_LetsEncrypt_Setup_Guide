**Setting Up Apache with Let's Encrypt SSL/TLS Certificate: Step-by-Step Guide for Fedora and Ubuntu**

1. **Installation of Required Packages:**
   **On Fedora:**
   ```
   sudo dnf install epel-release -y
   sudo dnf install httpd -y
   sudo dnf install certbot python3-certbot-apache -y
   ```
   **On Ubuntu:**
   ```
   sudo apt-get update
   sudo apt-get install software-properties-common -y
   sudo add-apt-repository universe
   sudo apt-get update
   sudo apt-get install apache2 -y
   sudo apt-get install certbot python3-certbot-apache -y
   ```

2. **Creation of Document Root Directory:**
   ```
   mkdir -p /var/www/html/example
   ```

3. **Configuration of Virtual Host on Fedora:**
   **Update Main Apache Configuration:**
   ```
   sudo vim /etc/httpd/conf/httpd.conf
   ```
   Add or update the `ServerName` directive:
   ```
   ServerName example.com
   ```
   Save the file and exit. Then restart Apache:
   ```
   sudo systemctl restart httpd
   ```
   **Create Virtual Host Configuration:**
   ```
   sudo vim /etc/httpd/conf.d/example.com.conf
   ```
   Add the following configuration:
   ```
   <VirtualHost *:80>
    ServerAdmin admin@example.com
    DocumentRoot /var/www/html/example
    ServerName example.com
    ServerAlias example.com

    ErrorLog /var/log/httpd/example_error.log
    CustomLog /var/log/httpd/example_access.log combined

    <Directory /var/www/html/example>
     AllowOverride All
     Require all granted
    </Directory>

    RewriteEngine on
    RewriteCond %{SERVER_NAME} =example.com
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
   </VirtualHost>
   ```
   Save the file and exit. Then restart Apache:
   ```
   sudo systemctl restart httpd
   ```

4. **Configuration of Virtual Host on Ubuntu:**
   **Update Main Apache Configuration:**
   ```
   sudo vim /etc/apache2/apache2.conf
   ```
   Add or update the `ServerName` directive:
   ```
   ServerName example.com
   ```
   Save the file and exit. Then restart Apache:
   ```
   sudo systemctl restart apache2
   ```
   **Create Virtual Host Configuration:**
   ```
   sudo vim /etc/apache2/sites-available/example.com.conf
   ```
   Add the following configuration:
   ```
   <VirtualHost *:80>
    ServerAdmin admin@example.com
    DocumentRoot /var/www/html/example
    ServerName example.com
    ServerAlias example.com

    ErrorLog ${APACHE_LOG_DIR}/example_error.log
    CustomLog ${APACHE_LOG_DIR}/example_access.log combined

    <Directory /var/www/html/example>
     AllowOverride All
     Require all granted
    </Directory>

    RewriteEngine on
    RewriteCond %{SERVER_NAME} =example.com
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
   </VirtualHost>
   ```
   Save the file and exit. Enable the site:
   ```
   sudo a2ensite example.com.conf
   sudo systemctl reload apache2
   ```

5. **Obtaining SSL Certificate:**
   ```
   sudo certbot --apache
   ```
   Follow the prompts provided by Certbot to complete the process.

6. **Setting Up Certificate Renewal:**
   ```
   sudo crontab -e
   ```
   Add the following line to schedule the renewal process:
   ```
   0 0 * * * /usr/bin/certbot renew --quiet
   ```
   This schedules the Certbot renewal process to run daily at midnight.

7. **Placeholder Replacement:**
   Replace all occurrences of "example" and "example.com" with your actual domain name.
