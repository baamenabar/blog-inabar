title: Create a Fly Image server from scratch in few minutes.\
subtitle: Setup the flyimage project to convert, compress (moz-jpeg), cache and serve your images on the fly using Docker on DigitalOcean.\
classes: language-markup\
startDate:2016-05-31T23:15:09+02:00\
pubDate:2017-01-08T21:18:01+01:00

<header>
<hgroup>
Images microservice with Docker on Digital Ocean
================================================

Use *flyimg* to handle all your images.
---------------------------------------

</hgroup>
</header>
Images are a big part of the web, and also the bigest part of the **web bloat**. Everyday I see sites way too heavy or way too complicated architectures for dealing with images. With [@sadok\_f](https://twitter.com/sadok_f) we have been working on having a simple server to deal with images. You request an image, give the desired dimensions and compression... and *<span lang="fr">voilà!</span>* you get your image back, cached and compressed using the incredible moz-jpeg.

DigitalOcean is not the only option you have, you can do it on Linode, AWS, even on a VPS or a regular server. But for this example, and since I have an account lying around we will use Digital Ocean.

*<span lang="fr">Mise en place</span>*, What we'll need {#ac2}
-------------------------------------------------------

-   A Digital Ocean account (the account itself it's free)
-   [flyimage](https://github.com/flyimg/flyimg) an image conversion/compression microservice project (take a look for now)
-   flyimg [Dropplet provision script](https://github.com/Flyimg/DigitalOcean-provision) for DigitalOcean.

New DigitalOcean Droplet {#ac3}
------------------------

Go to our DigitalOcean dashboard and just create a new droplet.

![](img/content/digi-ocean-docker-compose-create-droplet-button.png)

For the dropplet I'm chosing an Ubuntu 16.04 image (but it also worked with a 14.x). Choose the closest region to us or our audience.

![](img/content/digi-ocean-docker-user-data-field.png)

Check **User data** checkbox to have an initial config of users and libraries. We will take advantage of the **cloud-config** field available, where we can add YAML instructions to provision the server.

Here paste the contents of the [cloud-init-on-ubuntu.yml](https://github.com/flyimg/DigitalOcean-provision/blob/master/cloud-init-on-ubuntu.yml) file from the [**Flyimg Dropplet provision script**](https://github.com/Flyimg/DigitalOcean-provision) repository. If you are interested in server stuff [check the script](https://github.com/flyimg/DigitalOcean-provision/blob/master/provision-flyimg-on-ubuntu.sh), it's fully commented.

\#cloud-config\
users:\
- name: leopold\
groups: sudo\
shell: /bin/bash\
sudo: \['ALL=(ALL) NOPASSWD:ALL'\]\
package\_upgrade: true\
packages:\
- git\
- docker.io\
runcmd:\
- wget https://raw.githubusercontent.com/Flyimg/DigitalOcean-provision/master/provision-flyimg-on-ubuntu.sh\
- chmod 700 provision-flyimg-on-ubuntu.sh && ./provision-flyimg-on-ubuntu.sh

At the time of writing looks like the above (but check the repo for the latest version).

Also add your SSH key in the fields so you can ssh later to the server. I highly reccomend this because the provision script will close access to root and password login, only allowing access to the user "leopold" with your SSH key. If you don't have an SSH key this still works, but you can't finetune your server later on.

Finally just put a name to our server (in my case `flyimg`) and start the droplet.

Right after the image is created and the server started you can still `ssh` to our machine with root. Here you can take a peek at what is going on with the provisioning of the server.

ssh root@123.45.6.7

1.  \$ logged in the server\
    tail -f /var/log/cloud-init-output.log

Now you can see live what the robots are doing to setup your server. In the end you should see something like this:

![](img/content/digi-ocean-docker-provisioning-complete.png)

Optionally on a separate console window or session you can check that login in works with user **leopold**.

    ssh leopold@123.45.6.7

After that you can start using the server with the IP of the server, somehting like: `http://123.45.6.7`. But it would be prettier to have a domain to point it to.

Use a domain instead of an IP {#ac8}
-----------------------------

This is simple, you just need to tell you registrar (or DNS server provider) to create a DNS record type A pointing to the IP of your droplet, it should be available in a few minutes... or a few hours depending on the ISP, it requires propagation.

In this case we have the subdomain [img.medula.cl](http://img.medula.cl/) pointed to the server form the (mediatemple) control panel.

![](img/content/digi-ocean-docker-a-dns-record.png)

In dreamhost looks like the following:

![](img/content/digi-ocean-docker-a-dns-record-dreamhost.png)

Now you can try accessing the domain directly and you get the "Hello from Docker!" message.

Changing the defaults {#ac9}
---------------------

By default we can fetch images from any URL, resize, compress and serve them right back. That is easy to abuse. We will limit the domains from which the app can request images. Also I want to have a bunch of source images in this same domain.

To restrict the domain we need to edit `config/parameters.yml` file and change. For that you need to access your dropplet through SSH, and here is why you needed to add an SSH key at the creation of your dropplet.

    ssh leopold@123.45.6.7

The inside the server edit the `flyimg/config/parameters.yml` file (vim, nano, pico, whatever you want).

    leopold@flyimg:~$ vim flyimg/config/parameters.yml

Change the `restricted_domains` line

    restricted_domains: false

to

    restricted_domains: true

and add the domain or domains you want

    whitelist_domains:
        - medula.cl
        - m0.cl

There's no need to restart the server, we can just test by trying to fetch an image from flick, like this one: <https://c2.staticflickr.com/1/362/19293294393_cbde92e205_k.jpg> . If we pass it to the server <http://img.medula.cl/upload/w_201,h_201,q_90/https://c2.staticflickr.com/1/362/19293294393_cbde92e205_k.jpg> it returns a 403.

But if we use this image: <http://m0.cl/t/resize-test_760.jpg> and pass it to the server: <http://img.medula.cl/upload/w_502,h_501,c_1/http://m0.cl/t/resize-test_760.jpg> we get a 200 back with the image.

And **that is all** you ned to have your own little server resizing, compressing and caching your images.

Thaking it further {#ac10}
------------------

The next natural step is to make it serve using `https` for that you have at least 2 ways:

-   Difficult: Configure let's encrypt for your Ubuntu [following a tutorial from DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)
-   Easy: Use cloudflare to serve your images cached. Although you need to give control of you DNSs to Cloudflare.

Even cooler is to have your transformed images stored in a more permanent medium, like Amazon S3 or DigitalOcean block storage. **Flyimg** makes it simple because it uses [flysystem](http://flysystem.thephpleague.com/), A virtual file system that lets you use any storage and handle the files in the same way, like an ORM for file storage.

My next step is to have some sort simple system to organize my images, like a file system, but for images and the web only. I upload there, and use flyimg to do all the transformations and caching.

Links and credits {#ac3}
-----------------

-   [An Introduction to Cloud-Config Scripting](https://www.digitalocean.com/community/tutorials/an-introduction-to-cloud-config-scripting) by Justin Ellingwood. This helped me create the provisioning script for the host server.
-   [Initial Server Setup with Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04) by Justin Ellingwood. Here we setup basic config to the host machine.
