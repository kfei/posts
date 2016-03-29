+++
title = "CRUSH on single host"
date = "2014-06-11T03:36:00+08:00"
tags = ["ceph"]
+++

The CRUSH algorithm intends to distribute objects across different hosts, so if
you set up all OSDs on the same host, CRUSH will complians. For instance,
a Ceph cluster with 3 OSDs on single host will likely get a `65 active, 54
active+degraded, 73 active+remapped` state when fresh-deployed, which was
supposed to be `192 active+clean`.

<!--more-->

But sometimes we really expect it to work on just one host, in order to change
CRUSH's policy, get the current CRUSH map and add a simple fix, then push the
map back to cluster:

```
sudo ceph osd getcrushmap -o /tmp/crushmap
sudo crushtool -d /tmp/crushmap -o /tmp/crushmap.txt
sudo vi /tmp/crushmap.txt
# Find a line: "step chooseleaf firstn 0 type host",
# and change it to "step chooseleaf firstn 0 type osd".
sudo crushtool -c /tmp/crushmap.txt -o /tmp/crushmap.new 
sudo ceph osd setcrushmap -i /tmp/crushmap.new
``` 
