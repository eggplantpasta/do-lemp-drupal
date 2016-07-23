# do-lemp-drupal
Documentation for configuring a DigitalOcean droplet with a LEMP stack and Drupal 7.

* [Nginx Drupal recipe](https://www.nginx.com/resources/wiki/start/topics/recipes/drupal/)
* [MySQL](https://www.mysql.com/)
* [DigitalOcean](https://cloud.digitalocean.com/droplets)
*

## Create a droplet

* Choose an image. Ubuntu 14.04.4 x64
* Choose the $5 a month size.
* Do not add block storage.
* Choose a data centre region. San Francisco 1.
* Select additional options (backups)
* Add an [ssh key](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets)
* Give it a name (d7lemp)
* Create.

## Configure the server

* [Initial Server Setup with Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04)

### [Additional Recommended Steps for New Ubuntu 14.04 Servers](https://www.digitalocean.com/community/tutorials/additional-recommended-steps-for-new-ubuntu-14-04-servers)

Add firewall rules to allow ssh, http, https, and email. Show the rules, then enable.
```
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 25/tcp
sudo ufw show added
sudo ufw enable
```

Configure timezone.
```
echo "Australia/Perth" > /etc/timezone    
dpkg-reconfigure -f noninteractive tzdata
```

Set up time syncronisation.
```
sudo apt-get update
sudo apt-get install ntp
```

Do not enable a swap file.

Snapshot d7lemp-1.0

### [How To Install Linux, nginx, MySQL, PHP (LEMP) stack on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-14-04)

Install Nginx web server.

```
sudo apt-get update
sudo apt-get install nginx
```

Test by going to ip address output from the command below in a web browser.
```
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

Install MySQL

```
sudo apt-get install mysql-server
sudo mysql_install_db
sudo mysql_secure_installation
```

Install PHP for Processing

```
sudo apt-get install php5-fpm php5-mysql
sudo vi /etc/php5/fpm/php.ini
```

Configure the PHP Processor by editing the file php.ini
```
sudo nano /etc/php5/fpm/php.ini
```

Search for the line `; cgi.fix_pathinfo=1` and change it to `cgi.fix_pathinfo=0`.

Restrat PHP
```
sudo service php5-fpm restart
```

Configure Nginx to Use the PHP Processor.

```
sudo nano /etc/nginx/sites-available/default
```

Edit the file as per below
```
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root /usr/share/nginx/html;
    index index.php index.html index.htm;

    server_name server_domain_name_or_IP;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

Restart Nginx.

```
sudo service nginx restart
```

Create a test file.
```
sudo nano /usr/share/nginx/html/info.php
```

```php
<?php
phpinfo();
?>
```

```
sudo rm /usr/share/nginx/html/info.php
```
