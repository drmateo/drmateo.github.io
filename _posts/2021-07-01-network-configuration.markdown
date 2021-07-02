---
layout: post
title:  "Network configuration on Raspberry Pi"
date:   2021-07-01 19:56:00 +0200
categories: work setup
---

There are a few network configurations to put attentions first time of running Raspberry Pi after installing SO (Ubuntu 20.04) via Raspberry Pi Imager. Mainly, these are timezone, WLAN, change hostname and finally expose virtual name to resolve dynamically IP address of the machine. The goal of this post is to briefly show how to configure these 4 aspects. 

Note that the following will be necessary just in minimal OS deployments (like in Raspberry Pi) because the assistant of installation of a standard Ubuntu OS will set up this for us (fortunately).  

**Table of Contents**

- [Timezone](#timezone)
- [Netplan](#netplan)
- [HOSTNAME](#hostname)
- [Zeroconf Networking](#zeroconf networking)

## Timezone

`timedatectl` is a CLI tool for setting up date and time in an Ubuntu terminal OS. Can list timezones using the following command.

```bash
timedatectl list-timezones
```
Set timezone once discover the goal one is done by, 

```bash
timedatectl set-timezone TIMEZONE
```

Replace TIMEZONE according to your region, an example of TIMEZONE is `Europe/Madrid`

## Netplan

From version 20.04 of Ubuntu the network management has become something much more simple than used to be. This is because the introduction of `Netplan` utility, this tool manages system networks reading a YAML configuration file. Normally, the configuration file is allocated in the folder `/etc/netplan`. To check more information about Netplan go to [netplan.io](https://netplan.io/). An example of Netplan configuration file is the following,

```yaml
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        eth0:
            dhcp4: true
            optional: true
    version: 2
    wifis:
        wlan0:
            optional: true
            access-points:
                "SSID":
                     password: "PASSWORD"
            dhcp4: true
```

## HOSTNAME

Normally using the Pi imager you will get a device with a default HOSTNAME, to the date in which this post is written, that name is `ubuntu`. But this is sometimes annoying because this sort of devices is usually used inside of networks with multiple of them. For example suppose to have two robots, implemented each one with a Raspberry Pi, and one server station, also implemented with a Raspberry Pi, and you want them working together. One way to differentiate them inside of a network is defining their HOSTNAME. This section shows how to do this.

Change the name in the file `/etc/hostname`,

```
HOSTNAME
```

The other file to be changed is `/etc/hosts`. This file resolve the IP of specific host name. Note that the following segment of code in file `/etc/hosts` has a line `127.0.1.1 HOSTNAME` which `HOSTNAME` should be replaced with the defined name in `/ect/hostname`.

```
127.0.0.1 localhost
127.0.1.1 HOSTNAME

# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts

```

## Zeroconf Networking

Note that the above section shows a configuration file which resolve the IP of a HOSTNAME as a localhost. Let's keep the same example as before, the two robots and one server station, but this time their IP in the local network are assigned dynamically by DHCP. In this case we are forced to keep updating `/etc/hosts` file to keep update the relation between HOSTNAMEs and the IPs (in each of the actors inside the network), you will see soon that this is not scalable. So, which are the alternatives. Similar than DNS, Zeroconf Networking resolve LAN IPs of the associated to the host names. In Ubuntu the most popular implementation is Avahi. 

To install avahi, we use the normal procedure,

```bash
sudo apt update
sudo apt install avahi-daemon avahi-utils libnss-mdns
```

Zeroconf open implementation is controlled by setting the config file `/etc/avahi/avahi-daemon.conf`.

[//]:```
[//]:[server]
[//]:host-name=HOSTNAME
[//]:domain-name=local
[//]:```

```
[publish]
disable-publishing=no
disable-user-service-publishing=no
publish-address=yes
```

Once the configuration file is done, we restart the service as follows,

```bash
sudo service avahi-daemon restart
sudo update-rc.d avahi-daemon defaults
```