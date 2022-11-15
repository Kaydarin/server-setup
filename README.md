# Server Setup Guide

Below are the basic steps needed to setup the server.

## Web Server Installation

### 1. Nginx

- Install Nginx
  ```
  sudo apt install nginx
  ```
- Adjusting the firewall. The web server firewall is disabled by default. To enable the firewall, run the command below.
  ```
  sudo ufw enable
  ```
- Allow traffic from port 80 (normal, unencrypted web traffic), port 443 (TLS/SSL encrypted traffic) and port 22 (default port for SSH).
  ```
  sudo ufw allow 'Nginx Full'
  ```
  ```
  sudo ufw allow 'OpenSSH'
  ```
- Verify the firewall changes by running the command below

  ```
  sudo ufw status
  ```

  The output is as below

  ```
  Status: active

  To                         Action      From
  --                         ------      ----
  OpenSSH                    ALLOW       Anywhere
  Nginx Full                 ALLOW       Anywhere
  OpenSSH (v6)               ALLOW       Anywhere (v6)
  Nginx Full (v6)            ALLOW       Anywhere (v6)
  ```

- Check whether the web server is already running or not.
  ```
  sudo systemctl status nginx
  ```
  You should see something like this:
  ```
  ...
  Active: active (running) since Fri 2022-03-01 16:08:19 UTC; 3 days ago
  ...
  ```
- If it is already running, go to browser and enter the server's public IP address into the browser's address bar. You should see the default Nginx landing page.
  ```
  http://<your-server-public-ip>
  ```
- Next, create a new document root directory
  ```
  sudo mkdir -p /var/www/<your-domain-name>
  ```
- Assign ownership to the created directory
  ```
  sudo chown -R $USER:$USER /var/www/<your-domain-name>
  ```
- Grant permission to access the directory
  ```
  sudo chmod -R 755 /var/www/<your-domain-name>
  ```
- Create a sample html page for the site

  ```
  sudo nano /var/www/<your-domain-name>/index.html
  ```

- Copy and paste the code below into index.html file

  ```
  <html>
    <head>
        <title>Welcome!</title>
    </head>
    <body>
        <h1>Hello, World!</h1>
    </body>
  </html>
  ```

- Save and close the file by pressing Ctrl+X
- Create a server block or virtual host file to serve the content
  ```
  sudo nano /etc/nginx/sites-available/<your-domain-name>
  ```
- Paste the code below and press Ctrl+X to save the file

  ```
  server {
      listen 80;
      listen [::]:80;

      root /var/www/<your-domain-name>;
      index index.html index.htm index.nginx-debian.html;

      server_name <your-domain-name> www.<your-domain-name>;

      location / {
              try_files $uri $uri/ =404;
      }
  }
  ```

- Enable the server block and restart Nginx

  ```
  sudo ln -s /etc/nginx/sites-available/<your-domain-name> /etc/nginx/sites-enabled/
  ```

  ```
   sudo systemctl restart nginx
  ```

- Enable _server_names_hash_bucket_size_ in nginx.conf file to avoid possible hash bucket memory problem

  ```
  sudo nano /etc/nginx/nginx.conf
  ```

  Find the _server_names_hash_bucket_size_ directive and remove the '#' symbol to uncomment the line. Save and close the file by pressing Ctrl+X

- Run the command below to make sure there are no syntax errors in any of the Nginx files

  ```
  sudo nginx -t
  ```

- To enable the changes, restart Nginx.

  ```
  sudo systemctl restart nginx
  ```

- To test the results, go to browser and point to your server's public IP address. You should see the content that you have created.

## Managing Web Server Process

Below are the commands that we will usually use to manage our web server:

1. To stop the web server

   Nginx:

   ```
   sudo systemctl stop nginx
   ```

   Apache:

   ```
   sudo systemctl stop apache2
   ```

2. To start the web server when it is stopped

   Nginx:

   ```
   sudo systemctl start nginx
   ```

   Apache:

   ```
   sudo systemctl start apache2
   ```

3. To stop and then start the service again

   Nginx:

   ```
   sudo systemctl restart nginx
   ```

   Apache:

   ```
   sudo systemctl restart apache2
   ```

4. To re-enable the service to start up at boot

   Nginx:

   ```
   sudo systemctl enable nginx
   ```

   Apache:

   ```
   sudo systemctl enable apache2
   ```
