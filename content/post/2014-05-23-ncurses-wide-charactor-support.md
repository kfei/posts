+++
title = "ncurses wide charactor support"
date = "2014-05-23T08:35:00+08:00"
+++
Switch to libncurses**w** if needed.

```bash
cd /lib/x86_64-linux-gnu/
unlink libncurses.so.5
ln -s libncursesw.so.5 libncurses.so.5
```