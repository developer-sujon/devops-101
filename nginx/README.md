# Complete Guide to Setting Up Nginx Configuration

This document provides a step-by-step guide to set up Nginx for hosting applications on a new domain. We will configure SSL, add security headers, and redirect HTTP traffic to HTTPS.

---

## **Step 1: Prerequisites**
Ensure the following are installed and configured on your server:
- Ubuntu (latest version recommended)
- Nginx
- Certbot for SSL

---

## **Step 2: Install and Configure Nginx**

1. **Install Nginx:**
   ```bash
   sudo apt update
   sudo apt install nginx -y
   ```

2. **Create a Configuration File for Your Domain:**
   ```bash
   sudo nano /etc/nginx/sites-available/your-domain.com
   ```

3. **Add the Following Configuration:**
   Replace `your-domain.com` with your domain name.

   ```nginx
   # HTTPS server block
   server {
       listen 443 ssl http2; # Listen on port 443 for HTTPS
       server_name your-domain.com; # Replace with your domain name

       # SSL certificate files
       ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem;
       ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem;

       # Enforce modern SSL/TLS protocols
       ssl_protocols TLSv1.2 TLSv1.3;
       ssl_prefer_server_ciphers on;
       ssl_ciphers "ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256";

       # Security headers for hardening
       add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload";
       add_header X-Content-Type-Options "nosniff";
       add_header X-Frame-Options "DENY";
       add_header X-XSS-Protection "1; mode=block";
       add_header Content-Security-Policy "default-src 'self';";
       add_header Referrer-Policy "no-referrer";
       add_header Permissions-Policy "accelerometer=(), camera=(), microphone=(), geolocation=(), magnetometer=(), gyroscope=(), fullscreen=(self)";
       add_header custom-app-secret "YourCustomSecretValue"; # Replace with your custom value

       # Location block for serving the application
       location / {
           root /var/www/your-domain.com; # Path to your application's root directory
           index index.html;
           try_files $uri $uri/ =404;
       }

       # OCSP stapling for SSL optimization
       ssl_stapling on;
       ssl_stapling_verify on;
       resolver 8.8.8.8 8.8.4.4 valid=300s;
       resolver_timeout 5s;
   }

   # HTTP server block
   server {
       listen 80; # Listen on port 80 for HTTP
       server_name your-domain.com; # Replace with your domain name

       # Redirect all HTTP traffic to HTTPS
       return 301 https://$host$request_uri;
   }
   ```

4. **Enable the Configuration:**
   ```bash
   sudo ln -s /etc/nginx/sites-available/your-domain.com /etc/nginx/sites-enabled/
   ```

5. **Test the Configuration and Restart Nginx:**
   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```

---

## **Step 3: Obtain an SSL Certificate Using Certbot**

1. **Install Certbot:**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   ```

2. **Obtain the Certificate:**
   ```bash
   sudo certbot --nginx -d your-domain.com
   ```

3. **Verify Auto-Renewal:**
   ```bash
   sudo certbot renew --dry-run
   ```

---

## **Step 4: Test the Setup**

1. **Visit Your Domain:**
   Open `https://your-domain.com` in a browser to verify the setup.

2. **Check Headers:**
   Use `curl` to confirm the custom headers:
   ```bash
   curl -I https://your-domain.com
   ```

   You should see headers like:
   ```
   custom-app-secret: YourCustomSecretValue
   ```

---

## **Step 5: Repeat for Additional Domains**

To add a new domain, repeat steps 2 to 4 using the new domain name. Ensure the application and Nginx configuration point to the correct directory.

---

## **Basic Nginx Commands**

Here are some essential Nginx commands to manage the server:

1. **Start Nginx:**
   ```bash
   sudo systemctl start nginx
   ```

2. **Stop Nginx:**
   ```bash
   sudo systemctl stop nginx
   ```

3. **Restart Nginx:**
   ```bash
   sudo systemctl restart nginx
   ```

4. **Reload Nginx (Apply Configuration Changes):**
   ```bash
   sudo systemctl reload nginx
   ```

5. **Check Nginx Status:**
   ```bash
   sudo systemctl status nginx
   ```

6. **Test Nginx Configuration:**
   ```bash
   sudo nginx -t
   ```

7. **Enable Nginx to Start on Boot:**
   ```bash
   sudo systemctl enable nginx
   ```

8. **Disable Nginx from Starting on Boot:**
   ```bash
   sudo systemctl disable nginx
   ```
