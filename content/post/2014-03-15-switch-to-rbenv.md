+++
title = "Switch to rbenv"
date = "2014-03-15T07:36:00+08:00"
tags = ["ruby"]
+++

一直對 RVM 的 shell-hacking 行為很感冒也很困擾, 但是沒有了她又勢必崩潰在如滿天星斗的 Ruby 版本中...

決定從 RVM 轉到 rbenv (早該這麼做了).
先不管 rbenv 有什麼吸引人的 feature, 至少對比起 RVM 少了一些 dirty (IMO) hacks:

  - **RVM Need to be loaded into your shell.** Instead, rbenv's shim approach works by adding a directory to your `$PATH`. (這點超討厭!)
  - **RVM Override shell commands like cd or require prompt hacks.** That's dangerous and error-prone.
  - **RVM Have a configuration file.** There's nothing to configure except which version of Ruby you want to use.
  - **RVM Install Ruby.** You can build and install Ruby yourself, or use ruby-build to automate the process. (這個也很煩, Ruby binaries 我想自己管理就好, RVM 似乎有點撈過界...)
  - **RVM Manage gemsets.** Bundler is a better way to manage application dependencies. If you have projects that are not yet using Bundler you can install the rbenv-gemset plugin. (管理 gemsets 這種事還是交給專業的來吧...)
  - **RVM Require changes to Ruby libraries for compatibility.** The simplicity of rbenv means as long as it's in your `$PATH`, nothing else needs to know about it.
  
說了這麼多好像都是在批 RVM, 不過嘛... 工具這種東西就是這樣, 一定是越來越進步. 如同沒有 CVS, SVN 的難用, 就不會有好用的 Git 誕生一樣, 還是謝謝 RVM 陪伴了多年的時光, 功成身退.

ps. To uninstall all installed Ruby gems: `gem list | cut -d" " -f1 | xargs gem uninstall -aIx`
