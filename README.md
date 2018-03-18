# Docker Apache with SSL and phpBB installed

## 1. Export all your previous existing data from Mysql:
https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html

or

https://www.itworld.com/article/2833078/it-management/3-ways-to-import-and-export-a-mysql-database.html

## 2. Setting up SSL:
```bash
export SERVER_NAME='localhost'
mkdir ssl

openssl req -nodes -x509 -newkey rsa:2048 \
  -subj "/CN=$SERVER_NAME" \
  -keyout ssl/default.key \
  -out ssl/default.crt
```

## 3. Setting the execute flags to the bin directories stuff:
```bash
chmod +x /bin/download-phpbb
chmod +x /bin/phpbb-apache2
```

## 4. Change the options in the Dockerfile as you need:
DBHOST=192.168.2.205

DBPORT=3307

DBNAME=phpBB

DBUSER=default

TABLE_PREFIX=phpbb_

## 5. Building the Dockerfile locally
```bash
docker build . -f Dockerfile -t phpbb
```

## 6. Running the Docker container (Change "password" to your database password):
```bash
docker run -d \
-v "$PWD/ssl:/etc/apache2/ssl" \
-e SERVER_NAME=localhost \
-e DBPASSWD=password \
-p 443:443 \
--restart always \
--name phpbb \
phpbb
```

## 7. Access it via https://YourHostOrIp:443 (Change "YourHostOrIp" to your server address, of course)

## 8. Do the configuration manually (At least it did not work for me in another way):
You might need to delete the database and create a new one (phpBB says that tables with the database prefix already exists):
```SQL
DROP DATABASE phpBB;
CREATE DATABASE phpBB;
```

## 9. Remove the install directory from the docker container:
```bash
docker exec -t -i phpbb /bin/bash
# In the container navigate to:
cd /var/www/html/phpbb 
# Delete the install directory:
rm -rf install
```

## 10. Replay all your exported data (Same way as with the export but of course vice versa).

## Useful other stuff

### Remove all images and containers from Docker:
https://techoverflow.net/2013/10/22/docker-remove-all-images-and-containers/

```bash
# Delete all containers
docker rm $(docker ps -a -q)
# Delete all images
docker rmi $(docker images -q)
```

### GMX SMP data:
https://hilfe.gmx.net/pop-imap/pop3/serverdaten.html

mail.gmx.net

587


This image is based on https://github.com/blueimp/phpbb.