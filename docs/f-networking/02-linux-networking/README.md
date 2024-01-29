# Linux Networking

## Basic networking

### The ip command

You can find the ip address of a linux system using the `ip address`, `ip addr` or `ip a` command.

```bash
[arno@Server][~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether c4:18:88:aa:44:1c brd ff:ff:ff:ff:ff:ff
    altname enp0s18
    inet 10.0.0.2/24 brd 10.0.0.255 scope global eth0
       valid_lft forever preferred_lft forever
```

List all ip routes with `ip route`

```bash
[arno@Server][~]$ ip route
default via 10.0.0.1 dev eth0 proto static
10.0.0.0/24 dev eth0 proto kernel scope link src 10.0.0.2
```

You can add a static route using the following commands:
```bash
[arno@Server][~]$ ip route add x.x.x.x/x via x.x.x.x
```
```bash
[arno@Server][~]$ ip route add x.x.x.x/x dev eth0
```

### Traceroute

```bash
[arno@Server][~]$ traceroute bad.horse
traceroute to bad.horse (162.252.205.157), 30 hops max, 60 byte packets
 1  * * *
 2  * * *
 3  67.223.96.62 (67.223.96.62)  96.882 ms  96.855 ms  96.747 ms
 4  bad.horse (162.252.205.130)  97.376 ms  97.314 ms  97.447 ms
 5  bad.horse (162.252.205.131)  102.340 ms  102.290 ms  102.252 ms
 6  bad.horse (162.252.205.132)  107.451 ms  107.587 ms  107.320 ms
 7  bad.horse (162.252.205.133)  112.661 ms  110.908 ms  111.781 ms
 8  he.rides.across.the.nation (162.252.205.134)  117.483 ms  117.684 ms  117.302 ms
 9  the.thoroughbred.of.sin (162.252.205.135)  122.338 ms  122.115 ms  122.726 ms
10  he.got.the.application (162.252.205.136)  127.353 ms  127.467 ms  127.129 ms
11  that.you.just.sent.in (162.252.205.137)  132.447 ms  132.708 ms  132.079 ms
12  it.needs.evaluation (162.252.205.138)  137.700 ms  135.493 ms  135.455 ms
13  so.let.the.games.begin (162.252.205.139)  141.233 ms  142.372 ms  139.813 ms
14  a.heinous.crime (162.252.205.140)  146.967 ms  147.251 ms  147.232 ms
15  a.show.of.force (162.252.205.141)  152.208 ms  152.226 ms  152.186 ms
16  a.murder.would.be.nice.of.course (162.252.205.142)  157.779 ms  157.757 ms  157.744 ms
17  bad.horse (162.252.205.143)  162.813 ms  162.800 ms  162.786 ms
18  bad.horse (162.252.205.144)  167.865 ms  167.828 ms  167.797 ms
19  bad.horse (162.252.205.145)  172.586 ms  172.574 ms  172.217 ms
```

### DNS & domain lookup

You can use the `nslookup [ip]` command to test dns and find the ip address of a host.
Nslookup is not always installed on a linux system.

```bash
[arno@Server][~]$ nslookup google.com
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   google.com
Address: 142.251.36.14
Name:   google.com
Address: 2a00:1450:400e:80f::200e
```
An alternative to nslookup is `dig [ip]` (domain information groper)
```bash
[arno@Server][~]$ dig google.com

; <<>> DiG 9.18.18-0ubuntu0.22.04.1-Ubuntu <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 34191
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             287     IN      A       142.251.36.14

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Mon Jan 29 12:54:34 UTC 2024
;; MSG SIZE  rcvd: 55
```

- Commands
    - Nslookup
    - dig
    - Ping
    - Traceroute

## Networking config

Linux networking is configured using config files.
There are a couple files that a linux system uses.

Some examples are:
- /etc/network/interfaces
- /etc/netplan/xxx
- /etc/dhcpcd.conf

## netplan


```sh
[arno@Server][~]$ sudo netplan generate
[arno@Server][~]$ sudo nano /etc/netplan/*.conf
```

```yml
network:
    ethernets:
        eth0:
            addresses:
                - 192.168.1.212/24
            nameservers:
                addresses: [8.8.8.8, 8.8.4.4]
            routes:
                - to: default
                  via: 192.168.1.2
```

## vlan example

~~~yml
network:
    version: 2
    ethernets:
        ens3:
            addresses: 
                - 192.168.122.201/24
            gateway4: 192.168.122.1
            nameservers:
                addresses: [192.168.122.1]
        ens8: {}

    vlans:
        vlan.101:
            id: 101
            link: ens8
            addresses: [192.168.101.1/24]
        vlan.102:
            id: 102
            link: ens8
            addresses: [192.168.102.1/24]
~~~


```sh
sudo netplan apply 
```

### /etc/network/interfaces

```bash
[arno@Server][~]$ sudo nano /etc/network/interfaces
auto lo
iface lo inet loopback

iface eth0 inet static
        address 10.0.0.2/24
        gateway 10.0.0.1
#My network!
```

```bash
[arno@Server][~]$ sudo ifdown eth0 && sudo ifup eth0
```