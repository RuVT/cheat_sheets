# Web Site setup
This steps let you create a website usig Nginx (as web server) certbot and python3-certbot-nginx for creating the https certificates.

1. Update the packages (useful when starting in a new instance)
```bash
apt update
```

2. Install the packages
```bash
apt install nginx certbot python3-certbot-nginx
```

3. Create nginx config for the new site
```bash
# Create a new config file from the default
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/main-site

#Edit the file to look like this 
server {

        root /var/www/main-site;

        index index.html ;

        server_name your-domain.com www.your-domain.com;

        location / {
                try_files $uri $uri/ =404;
        }
}

# Create a link to enable the site
ln -s /etc/nginx/sites-available/main-site /etc/nginx/sites-enabled/
```

4. Create html file
```bash
# Create the directory
mkdir /var/www/main-site

# Create the html file
echo "
<!DOCTYPE html>
<html>
  <body>
    <h1>Hi</h1>
  </body>
</html>
" > /var/www/main-site/index.html
```

5. Restart nginx service
```bash
systemctl restart nginx
```

6. Enable certificates
```bash
certbot --nginx
# It will ask for an email for notifications
# Also it will ask you about what which domains need a certificated
```
