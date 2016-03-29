+++
title = "Build your own customized install disc from CentOS 6.4"
date = "2013-09-30T12:07:00+08:00"
tags = ["linux"]
+++

First, prepare a fresh-installed CentOS as your playground, choose the packages
that your customized disc like to install. Note that if you don't do so, the
`~/install.log` used in the following process will not exists.

<!--more-->

Create a working directory, e.g., `~/vt-iso`, and its sub directory
`~/vt-iso/isolinux/ks` to store kickstart config files,
`~/vt-iso/isolinux/postinstall` to store files for post-installation tasks.

Mount the CentOS install DVD disc 1 to `/mnt/cdrom`, copy all the files from
the `/mnt/cdrom/isolinux` directory into `~/vt-iso/isolinux`. And do not forget
the `/mnt/cdrom/.discinfo` file, it should be copied into `~/vt-iso/isolinux`
as well.

Gunzip the `comps.xml.gz` into `~/vt-iso/comps.xml`, in CentOS 6.4 it will be
`/mnt/cdrom/repodata/[UGLYHEXCODE]-c6-x86_64-comps.xml.gz`.

Now it's time to write down your kickstart config. Note that there are some
tricks in the following config: a variant-size issue for disk partitioning and
a baseurl issue for repository.

```bash
# Put these into kickstart config.
clearpart --all
part / --fstype=ext4 --size=4096
part swap --size 1024
# Let the partition grow as big as possible.
part /data --fstype=ext4 --size=1024 --grow
# Change the baseurl to /mnt/source or you'll get some repo not found error.
repo --name="CentOS"  --baseurl=file:///mnt/source --cost=100
```

*Optional:* Refine all packages that are really needed, compose your own
comps.xml and define some new groups.

Create a directory `~/vt-iso/all_rpms` and copy all RPMs under Packages
directory of CentOS disc 1,2 into, and then run mvRPM.sh to copy all RPMs that
are really needed into `~/vt-iso/isolinux/Packages`.

```bash
mvRPM.sh ~/install.log ~/vt-iso/all_rpms/ ~/vt-iso/isolinux/Packages
```

And don't forget to copy all extra needed RPMs into
`~/vt-iso/isolinux/Packages`, e.g. ruby19, git, dialog, pv, etc.

Then test the dependencies in your set of RPMs, if there are some problem, fix
it and go on.

```bash
mkdir /tmp/testdb
rpm --initdb --dbpath /tmp/testdb
rpm --test --dbpath /tmp/testdb -Uvh *.rpm
```

The repodata files provide information about the available packages, you have
to re-build them for your customized disc.

```bash
cd ~/vt-iso/isolinux
createrepo -u "media://`head -1 .discinfo`" -g ~/vt-iso/comps.xml .
```

*Optional:* Hack the anaconda installer. For instance, your boss ask you to
replace the CentOS logo or something like that, by the way, in this example it
will be in `/tmp/new_install/usr/share/anaconda/pixmaps/*.png`.

```bash
mount -o loop ~/vt-iso/isolinux/images/install.img /tmp/old_install
mkdir /tmp/new_install
rsync -av /tmp/old_install /tmp/new_install
# Do the dirty works.
mkfs.cramfs /tmp/new_install/ install.img.new
mv install.img.new ~/vt-iso/isolinux/images/install.img
```

*Optional:* Okay, if you also want to hack the `initrd.img`:
```bash
mkdir /tmp/initrd
# The compression method was changed to lzma since CentOS 6.4
lzcat ~/vt-iso/isolinux/initrd.img | ( cd /tmp/initrd && cpio -id )
# Do the dirty works. e.g. edit the .buildstamp file
cd /tmp/initrd
find . | cpio -co | lzma > /tmp/initrd.img.new
```

Now going to the most exciting step: Build the ISO and test it:
```bash
cd ~/vt-iso
mkisofs -o VT.iso -b isolinux.bin -c boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -R -J -v -T isolinux/
```

If the customized disc boot and install successfully, we can think further
about the pre/post installation tasks now. In my case, I have to setup my own
program (without RPM) to the customized installed system, so I do these things
during post-installation:

```bash
# Put these into kickstart config.
%post --nochroot
mkdir /mnt/sysimage/usr/sbin/wizard
cd /mnt/sysimage/usr/sbin/wizard
tar xjvpf /mnt/source/postinstall/wizard.tar.bz
echo "( cd /usr/sbin/wizard && /usr/bin/ruby19 repl )" >> /mnt/sysimage/root/.bashrc
%end
```
Note that the new root directory during post-installation is `/mnt/sysimage`,
and cdrom is mounted at `/mnt/source`. *Hint:* Mention the `--nochroot` flag.

And some other are:
```bash
# Put these into kickstart config.
%post
# Make an auto-login console.
sed -i -e 's/exec \/sbin\/mingetty $TTY/exec \/sbin\/mingetty --autologin root $TTY/g' /etc/init/tty.conf
# Append VGA boot parameter to kernel, it's an 1024x768 resolution.
sed -i '/kernel/ s/$/ vga=791/' /boot/grub/grub.conf
# Perhaps your boss want you replace something during grub.
sed -i '/^title/ s/title CentOS.*/title XXXXX/g' /boot/grub/grub.conf
%end
```

*Ref.*
1) http://smorgasbork.com/component/content/article/35-linux/128-building-a-custom-centos-6-kickstart-disc-part-1
2) http://systemdilettante.blogspot.tw/2013/04/playing-with-initrd.html
3) http://zhan.renren.com/mylinux?gid=3602888498025453823
