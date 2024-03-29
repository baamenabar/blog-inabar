title: Setup an images microservice server with Docker Compose\
subtitle:Guide for setting up and troubleshooting our first steps into using docker and docker compose for developing microservices or dev environments.\
classes: language-markup\
startDate:2016-04-18T22:59:24+02:00\
pubDate:2016-04-18T23:19:08+02:00

<header>
<hgroup>
Setup for an images microservice server using Docker and Docker Compose
=======================================================================

Setting up and troubleshooting our first steps into docker on Windows with Docker Machine.
------------------------------------------------------------------------------------------

</hgroup>
</header>
Yopu must probably be fedup already with all the talk about Docker, and how it is the future, how it's so easy to work with... Well for me it's just the means to an end. The end is to have a server that resizes and crops images on the fly, storing a cached version to avoid generating it each time. Currently, for this, you can use **cloudinary** or some other services, but I like to use open source alternatives and to **own my own my data**. We'll setup a little server to do so. The first step is done with this guide, get Docker working on your machine.

Actually, a simple docker instance is faily simple to have running, but it's not very flexible or extensible, we will be using some extra sugar to make our magic work. This guide is aimed at windows users, but for most of it, it works also for mac or linux users.

*<span lang="fr">Mise en place</span>*, the tools we will use {#ac1}
-------------------------------------------------------------

-   [Docker toolbox](https://www.docker.com/products/docker-toolbox) A bundled Docker environment
-   [fly-image](https://github.com/sadok-f/fly-image) (WIP) a image conversion/compression microservice project (take a look for now, we'll clone later)

Docker setup {#ac2}
------------

Download and install Docker Toolbox, I didn't have to change anything from the defaults, but check on your case.

You can run the **Docker Quickstart Terminal** that gets added to your start menu, you'll get a cute terminal looking like this:

![](img/content/docker-setup-first-console-output.png)

The cute whale lets you know you have started the default VM and all is ready to go. I din't like this terminal, so I switched to mine, but that is a matter of taste. Although the default VM is fine, we will do this with a little more complexity.

We will create an entirely new machine, as if you already had one virtual machine running with docker, but you want to leave it alone for work projects, this one will be for experimenting and testing things. Let's call it **munich** just because that's where I'm now.

    docker-machine create --driver virtualbox munich

You will get some output and now you can check if you have a virtualmachine ready to connect:

    docker-machine ls

I get a list with `default` and `munich`.

    docker-machine env munich

Will output a command you need to copy, paste and run:

What this means is that it's setting up the environment to be hooked to the machine you specified, meaning that the docker commands will be run only for that machine. You need to run this on every console window you open. It looks like a hastle, but you get used to it.

Docker Compose
--------------

The repo we are using depends on you having Docker Compose to run several containers to make the server work.

Cd into a folder and clone the repo:

     git clone git@github.com:sadok-f/fly-image.git
    # some output....
    cd fly-image

Now `docker-compose` will take care of everything.

    docker-compose build

The first time you run it, it will take a while to download and build the image, from then on, just seconds! The livong Docker promise of fast containers.

    docker-compose up -d

This will up the containers, after runing this, you can list your containers with

    docker ps

This command will be your best friend while working with docker.

Now you can connect to your machine, from a browser. For that you need your VM's IP (virtual machine's IP).

    docker-machine ip munich
    # will output something like: 
    192.168.99.101

with that IP you can make your first call to the fly-image server.

http://192.168.99.101:8080/upload/w\_500,h\_500,q\_90/https://www.mozilla.org/media/img/firefox/firefox-256.e2c1fc556816.jpg

If you get a file not found error...

![](img/content/docker-setup-file-not-found.png)

Troubleshooting
---------------

This is probably because the folder is not mounted. To comfirm we must access the fpm container, to do that we run a command interactively, which is pretty much the same connecting through SSH.

    docker exec -it fpm bash

Inside the container if you run `ls -la` and see an empty folder, then it's a **mounting problem**. If you cloned the fly-image repo outside your users folder (`C:\Users`) you must mount another volume by hand in VirtualBox and then again inside the VM. This gave me the most trouble, the simples solution is to wor in your Documents folder or even your desktop. If you want to go the hard route there are some links at the bottom.

If it's something else you can SSH into your virtual machine. The default password is `tcuser`.

    ssh docker@192.168.99.101

If you moved folders you need to run this process again, so you must stop the VM and start it again.

    docker-machine stop munich
    docker-machine start munich
    docker-machine env munich
    # paste whatever the previous output gives you
    docker-compose build
    docker-compose up -d

Now you check which containers you have running.

    λ docker ps
    CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
    ef30cd7be9a8        flyimage_nginx             "nginx -g 'daemon off"   11 days ago         Up 12 minutes       443/tcp, 0.0.0.0:8080->80/tcp   nginx
    3b31adbb55bd        flyimage_redis-commander   "redis-commander --re"   11 days ago         Up 13 minutes       0.0.0.0:8090->8081/tcp          redis-commander
    5815e1b8c6b8        flyimage_fpm               "php-fpm"                11 days ago         Up 13 minutes       9000/tcp                        fpm
    3d1d9b148bc9        flyimage_redis             "/usr/bin/redis-serve"   11 days ago         Up 13 minutes       6379/tcp                        redis

Here you get the name of your php-fpm container so you can run commands inside it and check if the mount is working fine.

    docker exec -it fpm bash

Inside the fpm container we need tu install the PHP dependencies with composer.

    composer update

This operation will take time, even fail a time or two, just run `composer update` again, it will pick up where it failed. If you keep getting a timeout error like the following.

     [Symfony\Component\Process\Exception\ProcessTimedOutException]
    The process "git clone --no-checkout 'https://github.com/sebastianbergmann/phpunit.git' '/var/www/html/vendor/phpunit/phpunit' && cd '/var/www/html/vendor/phpunit/phpunit' && git remote add composer 'https://github.com/sebastianbergmann/phpunit.git' && git fetch composer" exceeded the timeout of 300 seconds.

[Just increase the timeout](http://www.papayasoft.com/2013/08/22/composer-process-timeout-when-fetching-large-repositories/) to something still reasonable.

    COMPOSER_PROCESS_TIMEOUT=900 composer update

Once all that is cleared go visit this link (if the IP is the same of your VM): <http://192.168.99.100:8080/upload/w_333,h_333,q_90/https://www.mozilla.org/media/img/firefox/firefox-256.e2c1fc556816.jpg>

If you get an image back you are all set.

Daily usage
-----------

If you shut down your computer and need to start this server again you must run the following sequence:

    docker-machine start munich
    docker-machine env munich
    # you get a command to paste (in Cmnder it's @\@FOR /f "tokens=*" %i IN ('docker-machine env munich') DO \@%i@)
    docker-compose up -d
    If you always work on the same environment, you can make yourself an alias for the sequence.

Extra resources
---------------

### Mounting other volumes

-   [5 Useful Docker Tips and Tricks on Windows](http://blog.pavelsklenar.com/5-useful-docker-tip-and-tricks-on-windows/#attachment_165) By Pavel Sklenář.
-   [Docker: Permanently Mount a VirtualBox Shared Folder](http://www.developmentalmadness.com/2016/03/05/docker-permanently-mount-a-virtualbox-shared-folder/) By Mark J Miller
-   [Stack overflow](http://stackoverflow.com/questions/30040708/how-to-mount-local-volumes-in-docker-machine#answer-32030385)
