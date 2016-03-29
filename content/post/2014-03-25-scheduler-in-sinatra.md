+++
title = "Scheduler in Sinatra"
date = "2014-03-25T06:45:00+08:00"
tags = ["ruby", "sinatra"]
+++

Sinatra 上有些 webservice 要定時跑, 以前都是用 cron + curl 在做,
今天發現了一個好用的 gem: `rufus-scheduler`

<!--more-->
```ruby
require 'rufus/scheduler'

scheduler = Rufus::Scheduler.start_new

scheduler.cron '50 11 * * *' do
  # do something at 11:50 everyday...
end
```
