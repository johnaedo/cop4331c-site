---
share_cop4331c: "true"
site-folder: docs/Code Demos and Tutorials/LAMP Stack
---

## Setup a Virtual Host in Apache

### Create a config file for your site:
```bash
cd /etc/apache2/sites-available
cp 000-default.conf contacts-site.conf
```

### Edit your Site.  In this case, my Contacts app will be served by `contacts.johnaedo.com` and I'll place all of my app files in `/var/www/contacts`

Update the following settings:
- ServerName
- DocumentRoot
- Directory

> [!IMPORTANT]
> Don't forget the trailing slash on the directory you provided the \<Directory> container!

```xml
<VirtualHost *:80>  
	ServerName contacts.johnaedo.com  
	ServerAdmin webmaster@localhost  
	DocumentRoot /var/www/contacts  
  
	<Directory /var/www/contacts/>  
		Options Indexes FollowSymLinks  
		AllowOverride All  
		Require all granted  
	</Directory>  
  
	ErrorLog ${APACHE_LOG_DIR}/error.log  
	CustomLog ${APACHE_LOG_DIR}/access.log combined  
  
	<IfModule mod_dir.c>  
		DirectoryIndex index.php index.pl index.cgi index.html index.xhtml index.htm  
	</IfModule>  
  
	RewriteEngine on  
	RewriteCond %{SERVER_NAME} =lamp.johnaedo.com  
	RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]  
</VirtualHost>
```
### Enable Your Site

```bash
a2ensite contacts-site.conf
```

### Restart Apache

```bash
systemctl reload apache2
```
## Setup HTTPS/TLS

```bash
certbot
```
It should find your new domain name (e.g. `contacts.johnaedo.com`)
Select it when prompted and you will then have HTTPS enabled on your new site.

## Create Your Project Directory

```bash
cd /var/www
mkdir contacts
chmod 755 contacts
chown www-data:www-data contacts
```
Don't forget when copying files from your home (/root) directory that you will need to change their ownership to `www-data:www-data` and their permissions to `644` for files, `755` for directories.