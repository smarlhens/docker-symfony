# Docker containers (nginx, mariadb, php-fpm, maildev) for Symfony 4.x

<img src="https://github.com/smarlhens/docker-symfony/blob/symfony-4.x/docker-symfony.png" width="100%" alt="docker-symfony"/>

## Table of Contents 
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Building your development environment](#building-your-development-environment)
  - [Useful commands](#useful-commands)

- [What's in the box ?](#whats-in-the-box-)
  - [MailDev](#maildev)
  
---  

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites

What things you need to install the software and how to install them : 

* [Git](https://git-scm.com/)
* [Docker](https://docs.docker.com/docker-for-windows/install/) or [Docker Toolbox](https://github.com/docker/toolbox/releases)
  
---
  
### Installation

1. Clone the git repository

   ```bash
   git clone https://github.com/smarlhens/docker-symfony.git
   ```

1. Go into the project directory

   ```bash
   cd docker-symfony/
   ```

1. Checkout working branch

   ```bash
   git checkout <branch>
   ```

---

### Building your development environment

#### Docker

1. Copy environment files

   ```bash
   cp .env.dist .env
   ```

1. Create Docker images and launch them

   ```bash
   docker-compose up -d --build
   ```

We will now move on to the installation of the dependencies of the [Symfony project](#symfony-project).

---

#### Symfony project

Documentation : 
* [Symfony](https://symfony.com/doc/current/index.html)
* [Composer](https://getcomposer.org/doc/)

##### Install dependencies

You can execute commands inside container with the following command :

```bash
docker-compose exec <service> <command>
```
_Source : [docker docs](https://docs.docker.com/compose/reference/exec/)_

> You can execute your commands using **Bash** from the **php** container with the following command :
> ```bash
> docker-compose exec php bash
> ```

> **OR**

> You can execute your commands inside the **php** container with the following command :
> ```bash
> docker-compose exec php <command>
> ```

Then, execute these commands directly in the **php** container :

1. Composer

   ```bash
   composer install
   ```

---

##### Initialize project

The following commands must be executed directly in the **php** container.

1. Update DATABASE_URL in `.env` file

   ```bash
   DATABASE_URL='mysql://symfony:pa$$w0rd@db:3306/symfony'
   ```

1. Create database schema

   ```bash
   php bin/console doctrine:database:create
   ```

1. Execute all migration files that have not already been run

   ```bash
   php bin/console doctrine:migrations:migrate
   ```

1. *You can execute all the commands necessary to initialize your project now. Each project has its own command list and I only present the basic requirements.*

1. Clear cache

   ```bash
   php bin/console c:c -e prod
   php bin/console c:w -e prod
   ```

   You will need to remove ```-e prod``` for both command if you want to clear dev cache.

---

##### Access your website

Modify your hosts file 
* Linux: `/etc/hosts`
* Windows `C:/windows/system32/drivers/etc/hosts`

Add the line : 
```
<DOCKER_IP>    symfony.local
```

The Symfony project is now accessible at :
- [http://192.168.99.100](http://192.168.99.100) if you are using **Docker Toolbox**
- [http://<DOCKER_IP>]() if your are using **Docker**

---

### Useful commands

1. Dump database

   To execute the following command you need to be **outside** the Docker container.

   ```bash
   mkdir docker/db
   docker-compose exec db mysqldump -u symfony -ppa$$w0rd symfony > docker/db/symfony_dump.sql
   ```

1. If you want to check that all containers are up :

   ```bash
   docker-compose ps
   ```

1. Other Docker commands :

   ```bash
   # Start Docker
   docker-compose start
   
   # Restart Docker
   docker-compose restart
   
   # Stop Docker
   docker-compose stop
   
   # Delete all containers
   docker rm $(docker ps -aq)

   # Delete all images
   docker rmi $(docker images -q)
   ```
1. How to get a Docker container's IP address from the host ?

   ```bash
   docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container>
   docker inspect $(docker ps -f name=<service> -q) | grep IPAddress
   ```

---

## What's in the box ?

* `app` : container in which the application volume is mounted
* `php` : php container
* `db` : mariadb container in which the database is stored
* `nginx` : nginx container
* `maildev` : maildev container

### MailDev

MailDev is available at :
- [http://192.168.99.100:8000](http://192.168.99.100:8000) if you are using **Docker Toolbox**
- [http://<DOCKER_IP>:8000]() if your are using **Docker**

_Documentation : [MailDev docs](https://danfarrelly.nyc/MailDev/)_
