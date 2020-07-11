---
title: "registry"
date: 2020-07-11 21:48
---
[toc]





# docker registry



## installation



ubuntu 1804

```bash
cd /root
# install docker https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04
apt update
apt install apt-transport-https ca-certificates curl software-properties-common -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
apt update
apt install docker-ce -y
systemctl start docker

# install docker-compose https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-18-04
curl -L https://github.com/docker/compose/releases/download/1.26.2/docker-compose-Linux-x86_64 -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# install letsencrypt https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04
add-apt-repository ppa:certbot/certbot -y
apt update
apt install certbot -y

# Generate SSL certificate for registry
export DOMAIN="registry.xurick.com"
export EMAIL="admin@xurick.com"
certbot certonly --standalone -d $DOMAIN --preferred-challenges http --agree-tos -n -m $EMAIL --keep-until-expiring


# Setup letsencrypt certificates renewing 
cat <<EOF > /etc/cron.d/letencrypt
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
30 2 * * 1 root /usr/bin/certbot renew >> /var/log/letsencrypt-renew.log 
EOF


# https://community.letsencrypt.org/t/how-to-get-crt-and-key-files-from-i-just-have-pem-files/7348

mkdir /certs
cat /etc/letsencrypt/live/registry.xurick.com/fullchain.pem > /certs/fullchain.pem
cat /etc/letsencrypt/live/registry.xurick.com/privkey.pem > /certs/privkey.pem
cat /etc/letsencrypt/live/registry.xurick.com/cert.pem > /certs/cert.pem

apt install apache2-utils -y 
sudo mkdir -p /var/lib/docker/registry

htpasswd -Bbn testuser testpass  > ~/.htpasswd 


  
# https://docs.docker.com/registry/deploying/

docker run -d --name docker-registry --restart=always \
-p 443:5000 \
-v ~/.htpasswd:/auth_htpasswd \
-e "REGISTRY_AUTH=htpasswd" \
-e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
-e REGISTRY_AUTH_HTPASSWD_PATH=/auth_htpasswd \
-e REGISTRY_HTTP_ADDR=0.0.0.0:5000 \
-e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/fullchain.pem \
-e REGISTRY_HTTP_TLS_KEY=/certs/privkey.pem \
-v /certs:/certs \
-v /var/lib/docker/registry:/var/lib/registry \
registry:2
  
# List images
docker login https://registry.xurick.com
curl https://testuser:testpass@registry.xurick.com/v2/_catalog
```



```
docker tag postgres registry.xurick.com/rxu_postgresql:latest
docker push registry.xurick.com/rxu_postgresql:latest
```



```
$ docker container stop registry
$ docker container rm -v registry
$ docker container rm -f -v registry # Force remove running
```





## backup

```
/ # cat /etc/docker/registry/config.yml 
version: 0.1
log:
  fields:
    service: registry
storage:
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: /var/lib/registry
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3
```



backup this directory

```
cd /var/lib/docker/registry/

tar -zcf /tmp/docker.tar.gz docker
```



