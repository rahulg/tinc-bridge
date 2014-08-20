# Server Configuration

## Assumptions

* Your server has a static IP on the interface you're bridging.
* Your local subnet is 172.24.0.0/24, and the server is 172.24.0.1. Adjust accordingly.

## Network Configuration

You need to bridge `tinc`'s `tap` device with your ethernet. Here's an example:

Step 1: set your ethernet device to promiscuous mode.

```
ip link set dev eth0 promisc on
```

Step 2: create a bridge.

```
brctl addbr br0
brctl stp br0 on
```

Step 3: add the ethernet device to your bridge. Note that you will lose connectivity to the machine if you're connected to it over eth0 (in this example), so either use an out-of-band means to do this, or make steps 3 and 4 a script and run it within tmux/screen.

```
ip addr flush dev eth0
brctl addif br0 eth0
ip link set dev br0 up promisc on
```

Step 4: add the IP that used to be on `eth0` to `br0`.

```
ip addr add 172.24.0.1/24 brd 172.24.0.255 dev br0
```

## iptables

```
iptables -A FORWARD -i br0 -o br0 -j ACCEPT
```

## tinc Configuration

Refer to the tincvpn folder. Replace all keys with real ones. `tinc`'s documentation & man pages should tell you how to generate them.
