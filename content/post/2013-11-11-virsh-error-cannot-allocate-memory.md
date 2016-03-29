+++
title = "virsh error: Cannot allocate memory"
date = "2013-11-11T13:10:00+08:00"
tags = ["libvirt", "qemu", "vm", "linux"]
+++

Today I was trying to increase memory size on a qemu-kvm hosted VM. My plan was as simple as shut it down and then change memory setting via `virsh edit <VMName>`, but both `virsh edit` and `virsh start` throws the same error message:

> error: cannot fork child process: Cannot allocate memory

<!--more-->
It's weird because `free` shows me a sufficient amount of memory. But the fact is VM won't boot anymore and even edit its definition file is impossible. Google gives me an answer: `sysctl -w vm.overcommit_memory=1`, but it doesn't work.

```
# from kernel documentation
overcommit_memory:

This value contains a flag that enables memory overcommitment.

When this flag is 0, the kernel attempts to estimate the amount
of free memory left when userspace requests more memory.

When this flag is 1, the kernel pretends there is always enough
memory until it actually runs out.

When this flag is 2, the kernel uses a "never overcommit"
policy that attempts to prevent any overcommit of memory.
Note that user_reserve_kbytes affects this policy.

This feature can be very useful because there are a lot of
programs that malloc() huge amounts of memory "just-in-case"
and don't use much of it.

The default value is 0.
```

It does make sense! But seems qemu-kvm runtime parameter doesn't affected by `sysctl`, so I tried `echo 1 > /proc/sys/vm/overcommit_memory` and it just works!

Note that there are some useful actions for memory management:
```bash
# 3 means release all cache, including pagecache, dentries, inodes
echo 3 > /proc/sys/vm/drop_caches
# compact memory such that free memory is available in contiguous blocks
echo 1 > compact_memory
```

Still confused about why `free` tells me OK, but `virsh start` negative?

ref. https://www.kernel.org/doc/Documentation/sysctl/vm.txt

**Update 20131112**
Seems legacy libvirtd (in this case 0.12.0) has a memory leak issue under some situations, so `/etc/init.d/libvirtd restart` may solve this problem better. Upgraded to 1.1.1 and watching...
