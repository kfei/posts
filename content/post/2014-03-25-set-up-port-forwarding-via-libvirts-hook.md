+++
title = "Set up port-forwarding via libvirt's hook"
date = "2014-03-25T13:25:00+08:00"
tags = ["libvirt", "vm"]
+++

It's a bad idea using one file to maintain all port-forwarding rules for VMs.
In such a traditional environment (without SDN I mean), consider libvirt's hook
mechanism as a more modular way.

For Ubuntu, edit `/etc/libvirt/hooks/qemu` and then restart the `libvirt-bin`
service.
<!--more-->
```
# file: /etc/libvirt/hooks/qemu 
#!/bin/sh

GUEST_NAME=FiveFiveSixSix
HOST_PORT=5566
GUEST_IPADDR=5.5.6.6
GUEST_PORT=5566

if [ "$1" = "$GUEST_NAME" ]; then
  if [ "$2" = start ]; then
    iptables -t nat -A PREROUTING -p tcp --dport "$HOST_PORT" \
         -j DNAT --to "$GUEST_IPADDR:$GUEST_PORT"
    iptables -I FORWARD -d "$GUEST_IPADDR/32" -p tcp -m state \
         --state NEW -m tcp --dport "$GUEST_PORT" -j ACCEPT
  elif [ "$2" = stopped ]; then
    iptables -t nat -D PREROUTING -p tcp --dport "$HOST_PORT" \
         -j DNAT --to "$GUEST_IPADDR:$GUEST_PORT"
    iptables -D FORWARD -d "$GUEST_IPADDR/32" -p tcp -m state \
         --state NEW -m tcp --dport "$GUEST_PORT" -j ACCEPT
  fi
fi
```
