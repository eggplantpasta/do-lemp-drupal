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

Configure timezone and set up time syncronisation.
```
echo "Australia/Perth" > /etc/timezone    
dpkg-reconfigure -f noninteractive tzdata
```
