## Setup
### 1. Clone boilerplate
Cloning include specific configuration
```bash
cd /var/www/
git clone --recursive https://github.com/eldad87/sipmel_docker.git project-name

cd project-name
rm -rf app #Windows: Remove-Item -Recurse -Force docker\app
git clone --recursive https://github.com/eldad87/sipmel.git app
```

### 2. Setup Symfony
```bash
Create app/config/parameters.yml - need to match the DB settings in etc/environment*.yml
docker exec -it docker_app_1 bash
composer install
exit
```
Linux only!
```bash
sudo chmod -R 777 app/var/cache app/var/logs app/var/sessions
```

### 3. Add docker's domain to host file
Linux only!
```bash
sudo echo $(docker network inspect bridge | grep Gateway | grep -o -E '[0-9\.]+') "symfony.dev" >> /etc/hosts
```

### PhpStorm
##### Adjust permissions so PHPStrom can connect to out docker
```bash
https://techoverflow.net/2017/03/01/solving-docker-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket/
sudo usermod -a -G docker $USER
```
Try to import settings from documentation/phpstorm/settings.jar

##### xDebug
https://github.com/webdevops/php-docker-boilerplate/blob/master/documentation/DOCKER-INFO.md



## Useful commands
__Need to run inside /var/www/project-name/__
```bash
# Enter /var/www/project-name/
$ cd /var/www/project-name/
 
# bash commands
$ docker-compose exec app bash
 
# Composer (e.g. composer update)
docker-compose exec app composer update
 
# SF commands (Tips: there is an alias inside php container)
docker-compose exec app bin/console cache:clear 
 
# Retrieve an IP Address (here for the nginx container)
docker inspect --format '{{ .NetworkSettings.Networks.dockersymfony_default.IPAddress }}' $(docker ps -f name=nginx -q)
docker inspect $(docker ps -f name=nginx -q) | grep IPAddress
 
# MySQL commands
docker-compose exec mysql mysql -uroot -p"symfony"
 
# F***ing cache/logs folder
sudo chmod -R 777 var/cache var/logs var/sessions
 
# Check CPU consumption
docker stats $(docker inspect -f "{{ .Name }}" $(docker ps -q))
 
# Delete all images
docker rmi $(docker images -q)

# Stop all containers
docker stop $(docker ps -a -q)

# Delete all containers
docker rm $(docker ps -aq)
```

# Usefull
Change volume/images/containers/etc storage path
https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169
Bug about high storage consumption
https://github.com/moby/moby/issues/21925
https://lebkowski.name/docker-volumes/
