+++
title = "Directly access the file system inside a VM disk image"
date = "2013-10-23T08:40:00+08:00"
tags = ["linux", "vm"]
+++

Consider a situation that you want to change some settings i.e. edit some files
in VM, it'll be nice if you don't have to boot it up then log in to make some
changes. So the question becomes how to access the file system inside the VM
disk image file?
<!--more-->

To simplify the problem, assume you were using RAW format, if not, convert it by:
```bash
qemu-img convert old-hd.qcow2 hd.raw
```

Next step is to calculate the partition offset.
```bash
$ file hd.raw 
hda.raw: x86 boot sector; GRand Unified Bootloader, stage1 version 0x3, boot drive 0x80, 1st sector stage2 0x849f8, GRUB version 0.94; partition 1: ID=0x83, active, starthead 32, startsector 2048, 1024000 sectors; partition 2: ID=0x8e, starthead 221, startsector 1026048, 124803072 sectors, code offset 0x48
```

Look at the **startsector 2048** portion of output, this means that the
filesystem itself starts on sector 2048, and typically this is the /boot
partition. So to access the /boot partition you can mount the image with an
`offset` parameter, which in this case is 2048 times 512 (the size of
a sector), 1048576. For other partitions, `fdisk -l hd.raw` will be useful.
```bash
$ mount -o loop,offset=1048576 hd.raw /mnt/hdp1
```

Further, if there is LVM inside, partitions cannot be mounted using 'mount'. In
such a case the image could be mounted with: ```bash
$ vgscan
  Reading all physical volumes.  This may take a while...
  Found volume group "<VGName>" using metadata type lvm2
$ vgchange -ay
   2 logical volume(s) in volume group "<VGName>" now active
$ mount /dev/<VGName>/<LogicalVolumeName> /mnt/hdpN
```

ref. http://en.wikibooks.org/wiki/QEMU/Images#Mounting_an_image_on_the_host
