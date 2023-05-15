---
title: 'Running GRAV CMS in an appserver.io Docker container'
published: true
taxonomy:
    category:
        - tutorials
---

Running [GRAV CMS](https://getgrav.org/) on our appserver.io Docker container provides some 
advantages because some of the requirements that usually needs manual tweaks will be already 
set-up for you, like

* URL rewriting
* SSL support

So installing GRAV CMS in an appserver.io Docker container needs basically three steps.

> BTW: appserver.io website and documentation page run's on GRAV CMS. THANKS GRAV CMS team!!!

### Step 1: Docker Installation

Download and install [Docker](https://www.docker.com/community-edition) for your system.

If you already have a running Docker installation, you can skip this step and proceed with 
[Step 2](#step-2-starting-a-appserver-io-docker-container).

### Step 2: Starting a appserver.io Docker container

> If you already have a webserver running that listens either to one of the ports `80` and `443` 
> you'll also have to stop it temporarly.

We need a appserver.io container that runs the GRAV CMS instance. Again, open your commandline, if you've already closed it, and execute the following command

```sh
MacBook-Pro:~ docker run -d \
  -p127.0.0.1:80:80 \
  -p127.0.0.1:443:443 \
  -v /Users/wagnert/Workspace:/root/Workspace \
  --name appserver-1.1.4-grav \
  appserver/dist:1.1.4
```

This downlaods the latest stable appserver.io image and starts a container with the webserver and
PHP-FPM listening to the ports `127.0.0.1:80` and `127.0.0.1:443`.

### Step 3: Download and deploy GRAV CMS as PHAR archive

Beside the possiblity to copy the files to the webserver root directory, appserver.io also supports
a PHAR based [deployment](../../documentation/deployment). This
allows us to deploy GRAV CMS by downloading the sources - including all composer dependencies - as 
a PHAR archive and copy it to the deployment directory inside the Docker container. To do this,
execute the following lines on the commandline

```sh
MacBook-Pro:~ docker exec appserver-1.1.4-grav bash -c \
  "wget http://apps.appserver.io/grav/grav-latest.phar \
   -O /opt/appserver/deploy/grav-latest.phar"
MacBook-Pro:~ docker exec appserver-1.1.4-grav bash -c \
  "touch /opt/appserver/deploy/grav-latest.phar.dodeploy"
```

Now wait until appserver.io has deployed the GRAV CMS sources. To check if everything is ready, 
execute the following command several times and check the output

```sh
MacBook-Pro:~ wagnert$ docker exec appserver-1.1.4-grav bash -c "ls -l /opt/appserver/deploy"
total 195588
-rw-r--r-- 1 root root      4384 Jun  9 07:21 README.md
-rw-r--r-- 1 root root 200269499 Jun  9  0017 grav-latest.phar
-rw-rw-r-- 1 root root        44 Jun 10 15:23 grav-latest.phar.deployed
```
When the file `grav-latest.phar.deploying` changed to `grav-latest.phar.deployed` GRAV CMS 
has successfully been deployed. 

> The GRAV CMS deployment process could take up to 1 minute, depending on your hardware!

Finally, user your browser to open the GRAV CMS [frontend](http://127.0.0.1/grav-latest/) or 
[backend](http://127.0.0.1/grav-latest/admin/) and create the admin user.

That's it - have fun!
