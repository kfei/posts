+++
title = "A migration from Gitolite to Gitlab"
date = "2014-05-07T03:58:00+08:00"
tags = ["gitlab"]
+++

注意 [Gitlab](https://www.gitlab.com/) 這個專案很久了, 最近因為一些需求決定將公司所有的 Git repos 從 Gitolite 搬到 Gitlab 上.

一直以來都是以 **Gitolite + Redmine + Jenkins** 這套大三元作為開發及持續整合的環境, 耦合程度並不高, 因此將 Gitolite 抽換成 Gitlab 其實也只有一些 ~~小~~ 工作要做, 在此記錄一下.

## Git repos migration
```bash
# Set old url to read-only in Gitolite to avoid user still pushing their codes onto,
# then create new project and get its url in Gitlab.
git clone --bare <old_url>
# Optional: git gc --prune=now
cd <repo_dir> && git remote set-url origin <new_url>
git push --mirror origin
```

## Jenkins job trigger

有兩種方法可以做到. 一是設定 Gitlab 所提供的 *web hooks* 機制去觸發 Jenkins 工作; 二是用傳統的方法, 在 `<repo_path>/post-update` 裡寫 curl 指令. 前者需要注意的地方是 Gitlab 的 *web hooks* 一律都是 POST request, 而 Jenkins 的 remote trigger 卻只接受 GET request, 尷尬了! 為此我卡關了一點時間, 差點直接去改 Jenkins 的 code, 後來偶然發現 Jenkins 上的 Git plugin 其實也定義了一個界面供遠端呼叫, 例如: `curl http://<jenkins_server>/git/notifyCommit?url=<git_repo_url>` 可以觸發所有設定了 `<git_repo_url>` 的工作去執行一次 pulling SCM. 既然這樣, 那把所有 Job 的 build mode 從 remote trigger 改成 pulling SCM 不就好了? 是的! 就是這麼簡單.
至於第二種方法就沒什麼好特別說的了, 只要找到 Gitlab 把 repo 放在什麼路徑底下就可以了, 如果是在 Debian/Ubuntu 上使用 *Ominibus* package 安裝的話, 路徑會是 `/var/opt/gitlab/git-data/repositories/`.

## Redmine's project repository setting

值得一提的是, 以前總是在 Gitolite server 上的 bare Git directory 裡加上一條名為 Redmine 的 remote config, 然後在 *post-update hook* 裡寫上 `git push -f redmine <branch_name>` 之類的指令, 直接把 repo 同步到 Redmine server 上(Redmine server 上同時保存一份 bare directory), 然而這樣做其實有一個缺點, 就是會多出一條 redmine refs, 在某些地方(e.g. *gitweb*)上顯得很礙眼. 而直到這幾天我才發現, 原來 `git push <repo>` 裡的 repo 完全是可以顯式指定的, 因此在 Gitlab 上的 hook 只要寫好, 就不用再看到像是 `redmine/master` 這種沒意義的雜訊了!
```bash
# In <repo_path>/hooks/post-update, add:
git push -f <redmine_user@redmine_server:repo_path.git> *:*
```

## Pros

1. 透過 *merge request* 機制, 可以從工具層面保證 code review policy 的落實, 此外 diff tool 裡 line-by-line 的 comment 系統當然也是一大亮點.
2. 使用者帳號與 LDAP 完美結合(以往只有 key/mail 的 identity), 保證了與其他系統對接的一致性, 也更符合公司內部的使用情境.
3. 有一些實用的 APIs.
4. Project Owner 可以設定很多東西, 大量地減輕了原來 Git server admin 的瑣碎工作, 這真的很重要...
