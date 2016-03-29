+++
title = "清理那些不堪回首的 Git 歷史"
date = "2014-02-07T06:03:00+08:00"
tags = ["git"]
+++

Git commit comment 風格慣例統一之後, 遺留在 master 上那些不堪回首的 commit history 怎麼辦?
```bash
git checkout --orphan tmp <new_starting_point>
git commit -m "bye bye history"
git rebase --onto tmp <new_starting_point> master
git branch -D tmp
```
<!--more-->
