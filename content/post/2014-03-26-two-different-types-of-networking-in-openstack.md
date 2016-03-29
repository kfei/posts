+++
title = "Two different types of networking in Openstack"
date = "2014-03-26T06:03:00+08:00"
+++
Just found the following diagram when reading Openstack [documentation](http://docs.openstack.org/trunk/openstack-ops/content/network_troubleshooting.html), very intuitive. By the way I think the second picture has a mistake in *br-int* of the *Network Node*, the port `phy-br-eth1` should be `int-br-eth1`. Please corrects me if I'm wrong!

# Nova-Network Traffic in the Cloud
![network_packet_ping.png](http://user-image.logdown.io/user/4546/blog/4576/post/190546/r8w92b6GS4e5LLZBhzv4_network_packet_ping.png)

# OpenStack Networking Service Traffic in the Cloud
![neutron_packet_ping.png](http://user-image.logdown.io/user/4546/blog/4576/post/190546/xWuuwY5ORoKtktWLeVTB_neutron_packet_ping.png)