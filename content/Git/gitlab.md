---
title: "gitlab"
date: 2020-03-04 23:07
---
[toc]





# gitlab



## installation 

### Ubuntu

* 12.8.1

```
sudo apt update
sudo apt install ca-certificates curl openssh-server postfix

curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash

sudo apt install gitlab-ce -y

gitlab-ctl reconfigure
```



* enable ssl by Letsencrypt 

    Your domain’s Certification Authority Authorization (CAA) record must allow Let’s Encrypt to issue a certificate for your domain.

```
apt install letsencrypt -y

sudo letsencrypt certonly --standalone --agree-tos --no-eff-email --agree-tos --email rickxu1989@gmail.com -d git.xurick.com

sudo mkdir -p /etc/gitlab/ssl/
sudo openssl dhparam -out /etc/gitlab/ssl/dhparams.pem 2048

# After long time
chmod 600 /etc/gitlab/ssl/*

vim /etc/gitlab/gitlab.rb
external_url 'https://git.xurick.com'
registry_external_url 'https://registry.xurick.com'
nginx['redirect_http_to_https'] = true
nginx['redirect_http_to_https_port'] = 80
mattermost_external_url 'http://mattermost.xurick.com'
letsencrypt['enable'] = true
letsencrypt['contact_emails'] = ['rickxu1989@gmail.com'] # This should be an array of email addresses to add as contacts
letsencrypt['auto_renew'] = true
letsencrypt['auto_renew_hour'] = 12
letsencrypt['auto_renew_minute'] = "30"
letsencrypt['auto_renew_day_of_month'] = "*/7"

gitlab-ctl reconfigure
```

