---
layout: post
title: "Port forward"
category: "Utils"
tags: [windows,linux,tcp,ip]
---


### Windows ###
-----------------
```bash
netsh interface portproxy add v4tov4 listenport=8001 listenaddress=12.34.56.78 connectport=80 connectaddress=192.168.0.1
```

Explanation:

- Add a port proxy with `12.34.56.78:8001`, connnections to this address will be forward to `192.168.0.1:80`
- the `portproxy` support both `ipv4` and `ipv6`
- see details at [here](https://technet.microsoft.com/en-us/library/cc731068(v=ws.10).aspx)

Note:

> Make sure the ipv6 module is installed if you use the `portproxy`
>
> ```bash
> netsh int ipv6 install
> ```

This command is useful for docker on windows, userally, the docker machine on window only has a private network and use `NAT` to access the outside web, if you want to access the docker container service outside the host window, you can add a port forward on host to docker container.

For example, if you want to run docker [`kamon/grafana_graphite`](https://hub.docker.com/u/kamon/), you can start the container with this on windows

```bash
docker run \
  --detach \
   --publish=80:80 \
   --publish=81:81 \
   --publish=8125:8125/udp \
   --publish=8126:8126 \
   --name kamon-grafana-dashboard \
   kamon/grafana_graphite
```
It listens on port 80 for the web in docker machine, then you can forward the host port to that port 80 so the ourside of the windows can access the web now:

```bash
netsh interface portproxy add v4tov4 listenport=80 listenaddress=12.34.56.78 connectport=80 connectaddress=192.168.99.100
```

Note：

> assume the docker machine ip is 192.168.99.100

Then you can open one browse to open `http://12.34.56.78`

To delete the portproxy, use

```bash
netsh interface portproxy delete v4tov4 listenport=80 listenaddress=12.34.56.78 
```

### Linux ###
-----------------
On linux, there are many ip tools, here we use [`iptables`](http://ipset.netfilter.org/iptables.man.html) to forward the port

1. enable the filter in kernal

   ```bash
   echo 1 >/proc/sys/net/ipv4/ip_forward
   ```

   or

   ```bash
   vi /etc/sysctl.conf
   net.ipv4.ip_forward=1
   sysctl -p
   sysctl --system
   ```
   
2. create the port forward rule

   ```bash
   iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to 172.31.0.23:80
   ```
  
   if you have rule `DROP` for forward chain, you need to allow the forward incoming http:
 
   ```bash
   iptables -A FORWARD -i eth0 -p tcp --dport 80 -d 172.31.0.23 -j ACCEPT
   ```

3. display the status
   ```bash
   iptables -L -n -v --line-numbers
   ```


Where

- `-t`: table name, could be `nat`, `mangle`, `filter`
- `-A`: add a new rule
- `-p`: protocal, `tcp` or `udp`
- `-i`: the interface name
- `dport`: distination port
- `-j`: operation, could be `DROP`, `REJECT`, `ACCEPT`, `snat`, `dnat`
- `--to`: destination 
- `-L`: List rules
- `-v`: Display detailed information
- `-n`: Display IP address and port in numeric format
- `--line-numbers`: show line numbers

Here is the chain details:

- `PREROUTING`: Packets will enter this chain before a routing decision is made.
- `INPUT`: Packet is going to be locally delivered. It does not have anything to do    with processes having an opened socket; local delivery is controlled by the "local-delivery" routing table: ip route show table local.
- `FORWARD`: All packets that have been routed and were not for local delivery will  traverse this chain.
- `OUTPUT`: Packets sent from the machine itself will be visiting this chain.
- `POSTROUTING`: Routing decision has been made. Packets enter this chain just before handing them off to the hardware.

To understand the iptables, you first need to understand how the chain been applied for the different routes, here is the chian for different destinations:

1. Packages which target is local address 

   | Step | Table  | Chain      |
   |------|--------|------------|
   | 1    | mangle | PREROUTING |
   | 2    | nat    | PREROUTING |
   | 3    | mangle | INPUT      |
   | 4    | filter | INPUT      |

2. Packages which source is local address

   | Step | Table  | Chain       |
   |------|--------|-------------|
   | 1    | mangle | OUTPUT      |
   | 2    | nat    | OUTPUT      |
   | 3    | filter | OUTPUT      |
   | 4    | mangle | POSTROUTING |
   | 5    | nat    | POSTROUTING |

3. Packages which forwards to other destination

   | Step | Table   | Chain       |
   |------|---------|-------------|
   | 1    | mangle  | PREROUTING  |
   | 2    | nat     | PREROUTING  | 
   | 3    | managle | FORWARD     |
   | 4    | filter  | FORWARD     |
   | 5    | mangle  | POSTROUTING |
   | 6    | nat     | POSTROUTING |

A full state transimission is as below:
![rule flow](https://www.frozentux.net/iptables-tutorial/images/tables_traverse.jpg)


A full introduction could be found at [this](https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html), a chinese version is [this](https://www.frozentux.net/iptables-tutorial/cn/iptables-tutorial-cn-1.1.19.html)
