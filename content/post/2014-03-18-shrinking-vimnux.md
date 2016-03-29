+++
title = "Reduce disk usage on Debian-based distro."
date = "2014-03-18T11:23:00+08:00"
tags = ["linux"]
+++

前陣子因為公司產品上的需求, 客製了一套自己的 distro: V____x, 因為<del>我很懶</del>一些因素選擇直接從 Ubuntu Minimal 出發... 最終 Xorg, Openbox WM 加上公司產品及一些相依套件庫, 整個 OS disk usage 大約是 1.3G, 雖然對我來說還是太肥, 但安裝在 8G 硬碟的 thin client 上已經是綽綽有餘, 足以交差!

然而好景不常, 銷售團隊馬上接到任務, 客戶現有數百台搭配 1G CF 卡(實際約 900MB)的 thin client, 問 V____x 是否可以瘦身到 1G 以內並安裝上去?

於是就開始了緊急瘦身計劃.

ps. 這篇是事後補寫的, 因此有些數據只是憑印象.

Part 0:
雖然 `df -h` 看到 1.3G, 但實際透過 `du -sxh /` 發現檔案本身共佔約 1.1G 而已, 也就是大約減重 200MB 就可以了! 好像很簡單?

Part 1:
二話不說先進 /var/log 狂砍, 再殺到 /usr/share 看看有什麼 doc, manual, locale 之類的可以刪, 基本原則是只留 en locale!
```bash
$ cd /usr/share/locale
$ find ./ -maxdepth 1 -type d | tail -n +2 | sed -e 's/\.\///g' | \
      grep -E -v '^(en|currency|default|l10n).*' | \
      while read d; do rm -rf $d; done
$ localedef --list-archive | grep -E -v '^(en)' | \
      xargs localedef --delete-from-archive

$ cd /usr/share/i18n/locales
$ find ./ -name "??_*" | grep -E -v '(en)_' | while read f; do rm -f $f; 

$ cd /usr/share/man
$ find ./ -maxdepth 1 -type d | tail -n +2 | grep -E -v '(en|man).*' | \
      while read d; do rm -rf $d; done
```

Part 2:
再看看有什麼可以移除的套件, 包含 Ubuntu 自帶的和一些為了 build 自家產品裝的 *-dev 類套件! 直接透過
```bash
dpkg-query -Wf '${Installed-Size}\t${Package}\n' | sort -n
```
把目前系統所有已裝套件按佔用空間大小排序列出, 然後
```bash
aptitude -F'%p %I' search <package_name>
```
一一檢討吧! 怕誤砍可以搭配 `dpkg -L libgdbm-dev` 確認相關檔案.
```bash
apt-get purge --auto-remove <package_name>
```
幾乎把所有 *-dev 套件都刪光了, 另外還刪了 gcc, vim-runtime, geoip-database, 還有一些不記得了的等幾個比較佔空間的非必要套件(所謂佔空間其實不過也就幾 MB 大...), 在這一步驟應該可以搜刮出 100MB 左右.

看起來好像差不多了. 接下來是關於 file system 的故事...

Part 3:
**格式化.** 雖然 CF Card 還是搭配 FAT 使用為佳, 不過為了直接使用 rsync 搬移 OS 方便(畢竟只是客戶 POC), 還是把它格式化成 ext4 了! (因為 FAT 沒有 symlink 這種 file attribute, rsync migration 會失敗, 至於 journalized file system 在 CF Card 上會造成的諸多慘劇嘛... 改天再專題討論吧!)

這裡必須說明一下, 我嘗試透過 `tune2fs -O ^has_journal /dev/sda1` 將 ext4 上的 journal 取消, 不過一直失敗, 所以索性就設個小一點的 journal size 吧!
```bash
mkfs.ext4 -J size=4 /dev/sdb1
```
(這張 CF Card 實在太小, 因此 file system 上的 journal 必須關閉或儘量的小...)

到此事情還未結束, ext4 file system 本身的 overhead 大約 1.5%, 也就是說實際可用空間將只剩下 900MB * 0.985 = 886MB, 好不容易東省西省得來的幾 MB 就這樣花掉了, 真的是捉襟見肘! 因此 `mkfs.ext3 -J size=4 /dev/sdb1` 改用 ext3...

Part 4:
**搬家.** 因為 V____x 是綁定硬體發行的, 所以一直沒做 installer, 因此就直接 rsync 硬搬:
```bash
rsync -aAXv /* /mnt/sdb1 --exclude={/dev/*,/proc/*,/sys/*,/tmp/*,/run/*,/mnt/*,/media/*,/lost+found}
# Alternative: tar -C / -clspf - . | tar -xlspvf -
```

Part 5:
**把 GRUB 裝到 CF Card 上.** `bldid` 確認一下 CF Card 的 UUID, 視需要改一下 /mnt/sdb1/etc/fstab. 然後:
```bash
mount -o bind /dev /mnt/sdb1/dev
mount -t proc none /proc /mnt/sdb1/proc
chroot /mnt/sdb1 /bin/bash
update-grub && grub-install /dev/sdb
```
(這邊可能會有小雷, 如果得到空間不足, GRUB image 裝不下去的錯誤, 可以重新切一下 partition, 在第一個 partition 前空出足夠空間給 GRUB 擺.)

Part 6:
把 CF Card 裝回 thin client, 確認開機正常, 打完收工!

ref.
https://wiki.ubuntu.com/ReducingDiskFootprint
http://jamyy.dyndns.org/blog/2012/07/3938.html
