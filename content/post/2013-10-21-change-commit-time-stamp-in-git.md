+++
title = "Change commit time stamp in GIT"
date = "2013-10-21T18:01:00+08:00"
tags = ["git"]
+++

由於寫 code 專用的 VM 有時候會被我 pause, 然而 resume 後時間往往會跑掉,
這時候要是做了 commit 就會出現新 commit 的時間比舊 commit 的時間更早的怪事,
像這樣:

```
* ee0daaf 2013-10-16 | swap method2 and method3 (HEAD, master) [kfei]
* a1544cc 2013-10-21 | completes VD Center setup (origin/master, origin/HEAD) [kfei]
* 0a4e123 2013-10-03 | modify env check process about qemu [kfei]
...
```

<!--more-->

可以透過 amend 加上 `--date` 參數的方式來修改錯誤的 commit time stamp:

```bash
git commit --amend --date="Tue Oct 22 01:56 2013 +0800"
```
