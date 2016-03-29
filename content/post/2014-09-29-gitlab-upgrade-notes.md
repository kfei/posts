+++
title = "Gitlab upgrade notes"
date = "2014-09-29T08:17:00+08:00"
tags = ["gitlab"]
+++

*Updated for Omnibus 7.6.2 package - 20150128*

Note that for an **omnibus installation**, Gitlab files are under `/var/opt/gitlab` and `/opt/gitlab/embedded/service`.

Before any action, stop services that may change database:
```bash
gitlab-ctl stop unicorn
gitlab-ctl stop sidekiq
gitlab-ctl stop nginx
```

<!--more-->

Create a backup (will result in a tar ball under `/var/opt/gitlab/backups/`):
```bash
gitlab-rake gitlab:backup:create
cp /etc/gitlab/gitlab.rb /etc/gitlab/gitlab.rb.bak
```

Install new version, which automatically remove the previous version:
```bash
dpkg -i ${NEW_VERSION}.deb
```

Remove previous configs in case you have some customized settings.
```bash
gitlab-rake gitlab:backup:restore
# BACKUP=timestamp_of_backup is optional
```

Restore configs:
```bash
mv /etc/gitlab/gitlab.rb.bak /etc/gitlab/gitlab.rb
```

Start the upgrade process (including database migrations):
```bash
gitlab-ctl reconfigure
```

(Maybe) If reconfiguration throws some exception about postgresql:
```bash
gitlab-ctl start postgresql
```

(Maybe) Restore hacked files, e.g., logo_white.png, favicon.ico, etc.
```bash
cp /path/to/customized/logo-white.png /opt/gitlab/embedded/service/gitlab-rails/public/assets/logo-white-xxxxxxx.png
gitlab-rake cache:clear RAILS_ENV=production
```

Restart Gitlab instance and check if it works:
```bash
gitlab-ctl restart
```

