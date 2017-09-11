# Sipmel #
Simplifying control over multimedia communication.

## Goals ##
- Provide a unified API for communication via any channel (SMS, Email, Skype etc.).
- Persona management
	- Contact list (each list need to belong to a group, a persona need to be unique per group)
	- User timezone by country, fallback by contact detail (i.e phone country code)
	- Segment each contact's channel (Phone, Email, Skype account etc.) with a verification status:
		- None - Default status
		- Verified - Once passed some internal clearance
	- Rate each contact's channel with a status:
		- Sent: int - amount of messages been sent
		- Delivered: int
		- Undelivered
		- Replay: int - amount of incoming messages
		- Clicked: int - amount of clicks result from this channel
		- Conversion: int - amount of conversions result from this channel
- Content management
	- Create plain text/HTML messages
	- Multiple variants of the same content
		- Send appropriate content by language
	 	- If there is more then 1 variant of the same language - will be prioritized by results
	- Rate each content variant:
		- Used: int
		- Replay: int
		- Clicked: int
		- Conversion
	- Contact provider (Gmail, Hotmail, Verizon etc.)
	- Variables
		- Create placeholders in your templates
		- Variable behavior can be defined globally or per template
		- Variable behavior can be
			- Value override
			- Default value if not provided
			- Default value if empty
			 
- Contact Verification
	- Check contact detail against external sources to determine if its a valid channel (Valid, DNS, Reporter, invalid etc.)
	- Check who is the provider of that contact (Gmail, Voda phone, Verizon etc.)
- Campaign management
	- Select contact list + contact status (verified, replayed, clicked etc.)
	- Sequence of messages (content#1 today, content#2 tomorrow etc.)
	- Working hours, days and countries
	- Rules engine
		- Q: What to do if user clicked
		- Q: What to do if user didn't showed any action in last N# engagements etc.)
		- A: Move to campaign X
		- A: Freeze
- Auto optimization, Use better converting more often
	- Content variants, break down by:
		- Country
		- Channel type
		- Recipient Channel Provider (Gmail, Verizon etc.)
	- Sender senders, break down by:
		- Country
		- Recipient Channel Provider (Gmail, Verizon etc.)
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
 
# Delete all containers
docker rm $(docker ps -aq)
 
# Delete all images
docker rmi $(docker images -q)
```

## Info
A docker/symfony docker using [This PHP docker boilerplate](https://github.com/webdevops/php-docker-boilerplate) and [This PHP docker boilerplate](https://github.com/maxpou/docker-symfony)

