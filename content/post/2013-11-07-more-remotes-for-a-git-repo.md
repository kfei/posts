+++
title = "More remotes for a GIT repo"
date = "2013-11-07T06:05:00+08:00"
tags = ["git"]
+++

We know we can add some other remotes and its url after an initial `git remote
add origin`, and one more trick is that we can do some group aliases such as:

```
# <repo>/.git/config
[remote "mars"]
        url = <url-mars>
        fetch = +refs/heads/*:refs/remotes/origin/*
[remote "origin"]
        url = <url-origin>
        fetch = +refs/heads/*:refs/remotes/origin/*
[remote "all"]
        url = <url-mars>
        url = <url-origin>
```

<!--more-->
Then we can push to 2 remotes via one command like `git push all master:master`.
