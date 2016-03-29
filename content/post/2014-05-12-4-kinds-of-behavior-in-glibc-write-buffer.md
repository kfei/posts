+++
title = "4 kinds of behavior in glibc write buffer"
date = "2014-05-12T06:02:00+08:00"
+++
如果不強制呼叫 `fflush()` 來將 userspace buffer 寫入 kernel buffer 裡的話, glibc 有以下幾種預設的行為:
    - full buffer for disk file, 當緩衝區全滿或者 fd close 時寫入
    - row buffer for tty, 當遇到換行符時寫入
    - no buffer for stderr, 不緩衝, 每個 printf 都直接對應一次寫入
