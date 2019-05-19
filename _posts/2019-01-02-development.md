---
layout: post
title: Create Development VPS
---

Here you create a development environment on an Ubuntu Server 18.04 LTS.

## Install Nginx

Update system:

```
sudo apt update
sudo apt upgrade
```

Open ports TCP/80 and TCP/443 in firewall:

```
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
sudo apt install iptables-persistent
```

Install Nginx:

```
sudo apt install nginx
```

Edit default site definition:

```
sudo vi /etc/nginx/sites-available/default
```

Enter actual server name. For example:

```
server_name www.example.com;
```

Restart Nginx:

```
sudo systemctl restart nginx
```

## Install Free SSL Certificate

Add personal package archive (PPA):

```
sudo add-apt-repository ppa:certbot/certbot
```

Press Enter to confirm addition.

Install packages:

```
sudo apt update
sudo apt upgrade
sudo apt install certbot python-certbot-nginx
```

Run Certbot:

```
sudo certbot --nginx
```

1. Enter email address
2. Enter **a** for agree
3. Enter **y** or **n** for email address sent to Electronic Frontier Foundation
4. If a list of domain names appears, enter the number of your domain name from the list
5. Enter **2** if you want to force HTTPS

Set up Certbot to do renewal every 90 days:

```
sudo certbot renew --dry-run
```

Open browser and do initial test of HTTPS version of website. You see message, "Welcome to nginx!"

## Install MariaDB

Install packages:

```
sudo apt install mariadb-server
```

Secure MariaDB:

```
sudo mysql_secure_installation
```

1. There is no current password for root, so press Enter
2. Enter **n** to set root password, since we will use unix socket authentication for root
3. Enter **y** to remove anonymous users
4. Enter **y** to disallow root login remotely
4. Enter **y** to remove test database and access to it
6. Enter **y** to reload privilege tables now

## Install PHP

Install basic PHP packages:

```
sudo apt install php-fpm php-mysql
```

Edit PHP initialization file:

```
sudo vi /etc/php/7.2/fpm/php.ini
```

Set default timezone:

```
date.timezone = UTC
```

## Configure Nginx for PHP

Edit default site:

```
sudo vi /etc/nginx/sites-available/default
```

Add index.php as first possibility for index file:

```
index index.php index.html index.htm index.nginx-debian.html;
```

Uncomment PHP location block, and make sure unix socket file name corresponds to PHP version:

```
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
    }
```

Restart Nginx:

```
sudo systemctl restart nginx
```
 
Build test page:

```
sudo vi /var/www/html/index.php
```

Insert contents:

```
<?php
  phpinfo();
?>
```

Test HTTPS index page. You see PHP info page.

## Install Mail Server

First, add a DNS `A` record pointing to your server, e.g. `smtp.example.com` points to IP address `3.4.5.6`.

Second, add a DNS `MX` record pointing to e.g. `smtp.example.com` with priority `10`.

Third, set up Sender Policy Framework (SPF) for your domain by adding a DNS `TXT` record. The name of the entry is the naked domain. (Some DNS services use a blank, while others use a commercial-at sign for this.) The value of the text field is:

```
"v=spf1 mx -all"
```

This specifies that you will allow the domain's MX server(s) to send email for the domain, but you want to prohibit all other servers from sending email on your behalf.

Open the firewall to allow mail in on port 25:

```
sudo iptables -A INPUT -p tcp --dport 25 -j ACCEPT
sudo dpkg-reconfigure iptables-persistent
```

Install mutt and postfix:

```
sudo apt install mutt postfix
```

During the installation, the Postfix Configuration screen appears, informing you of the possible types of Postfix mail server you can have. Tab to **Ok** and press Enter. On the next screen, the actual choices appear. An **Internet Site** (which can both send and receive email directly using SMTP) is the default. Tab to **Ok** and press Enter. On the next screen, you are asked to enter your domain name. This will be your domain name, e.g. `example.com`. Enter your domain name, tab to **Ok**, and press Enter.

Edit the main Postfix configuration file:

```
sudo vi /etc/postfix/main.cf
```

Change the line for myhostname to match your actual hostname. For example:

```
myhostname = smtp.example.com
```

Duplicate the first three lines for `smtpd` (incoming) TLS configuration, then change the copied lines so that they specify `smtp` (outgoing) TLS:

```
smtp_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
smtp_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
smtp_use_tls=yes
```

Also add a line at the end of the file that reads:

```
home_mailbox = Maildir/
```

Write the file to disk, and quit the editor.

Now that everything is ready, restart Postfix:

```
sudo systemctl restart postfix
```

Edit your personal Mutt configuration file, using either the vi editor or the vi editor, as you prefer:

```
vi ~/.muttrc
```

Here is a Mutt configuration file you can use to get started (and which you can change as you learn more about Mutt):

```
set mbox_type=Maildir
set folder="~/Maildir"
set mask="!^\\.[^.]"
set mbox="~/Maildir"
set spoolfile="~/Maildir"
set sort=reverse-date-received
set sort_aux=reverse-date-received
set editor='vi'
```

Write the file to disk.

Make an initial Maildir directory for yourself:

```
mkdir ~/Maildir
```

Edit the Mutt global colors file:

```
sudo vi /etc/Muttrc.d/colors.rc
```

Replace every occurrence of `black` by `default` to improve the appearance of Mutt.

```
:%s/black/default/g
```

Write the file to disk, and quit the editor.

You can now invoke Mutt for an initial test with the command:

```
mutt
```

Since there is no mail as yet, you will get a message, `/home/ubuntu/Maildir is not a mailbox`. This is just a warning.

Visit [https://mxtoolbox.com](https://mxtoolbox.com) to test your mail server set up.

## Install PHP Composer, NodeJS, and NPM

Install prerequisites:

```
sudo apt install curl git unzip
sudo apt install php-cli php-xml php-json php-mbstring php-tokenizer php-bcmath
```

Install PHP composer:

```
cd ~
curl -sS https://getcomposer.org/installer -o composer-setup.php
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
sudo chown -R ubuntu:ubuntu .composer
```

Install NodeJS and NPM:

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt install nodejs
node -v
npm -v
```

## Install Laravel

```
composer global require laravel/installer
```

Add `$HOME/.config/composer/vendor/bin` to your `PATH`.

## Create New Laravel Project

```
cd /var/www/html
laravel new repo-name
```

## Configure Nginx for Laravel

Edit Nginx default site:

```
sudo vi /etc/nginx/sites-available/default
```

Change Nginx webroot to `/var/www/html/repo-name/public`:

```
root /var/www/html/repo-name/public;
```

Insert `index.php` into URLS:

```
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

Write Nginx default configuration to disk.

Restart Nginx:

```
sudo systemctl restart nginx
```

## Configure Laravel Project

Copy the the sample environment variable file to its final destination:

```
cd repo-name
cp .env.example .env
```

Generate an app encryption key:

```
php artisan key:generate
```

Edit the environment file:

```
vi .env
```

Set value for APP_URL. For example:

```
APP_URL=https://www.example.com
```

Write the environment variable file to disk.

Change group to web server:

```
sudo chgrp -R www-data /var/www/html/repo-name/public
```

where `ubuntu` is your user name on the Ubuntu server.

## Test

Test access to site. If there are errors:

```
sudo tail /var/log/nginx/error.log
```
