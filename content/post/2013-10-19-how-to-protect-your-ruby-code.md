+++
title = "How to protect your Ruby code?"
date = "2013-10-19T09:01:00+08:00"
tags = ["ruby"]
+++

前陣子寫了一包產品部署配置的 Ruby 腳本, 老闆問: **混碼**了沒?

> 老闆什麼都想混碼!

於是簡單 survey 了一下確實發現有一些作法, 不過也不太成熟就是了.
但這整件事 make sense 嗎? Do you really want to marshal a scripting language?
我覺得還倒不如把 Ruby code 轉成 Brainfuck 算了?

鬼才看得懂!

於是再找了一下, 發現真的有像 [C2BF](http://esolangs.org/wiki/C2BF) 這樣的 converter, 可以把 C 的原始碼轉換成 Brainfuck code.

```bash
cd ${HOME}
svn co https://c2bf.svn.sourceforge.net/svnroot/brainfuck/c2bf/trunk c2bf
cd c2bf
# install bison flex according to your distro.
YACC=bison make
```

雖然不可能因為這樣就真的把 project 用 C 重寫(原始碼行數至少百倍吧),
但這個思考方向確實挺有趣的! 有機會可以來試試笑果.

**有離職工程師不爽交接把專案程式碼轉成 Brainfuck 的八卦嗎?**
