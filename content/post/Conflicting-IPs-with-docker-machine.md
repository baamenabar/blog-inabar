title:Conflicting IPs with docker machine\
subtitle:Short tip for debugging\
classes: language-markup\
pubDate:2017-01-18T07:24:13-01:00

Conflicting IPs with docker machine
===================================

If you are using multiple machines with docker-machine they will get an automatic IP starting from 192.168.99.100 , then 192.168.99.101 and so on. The problem comes when you restart your system and start the machines in different order. Virtualbox will assign different IPs this time around, this makes the certificates invalid and you need to regenerate them. No problem there, but if you have mapped an IP to a domain in your hosts file, then trouble begins.

You can’t really control which IP virtualbox will assign to the machine; see https://github.com/docker/machine/issues/1709 . So you can not assign a specific IP to a docker-machine virtualbox vm.

But you can give it a range and assume it will fall in the 192.168.x.100 pattern. Use the following command when creating.

    docker-machine create -d virtualbox --virtualbox-hostonly-cidr "192.168.98.1/24" victor

So when you restart your computer, they will respect the range and usually will fall in the .100 IP

Also consider other workarounds presented in that thread, like this script: https://github.com/fivestars/docker-machine-ipconfig
