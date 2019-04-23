---
title: "httpd"
date: 2019-04-21 12:10
---


[TOC]



# httpd



## ssl



Below is a very simple example of a virtual host configured for SSL. The parts listed in bold are the parts that must be added for SSL configuration:

```
<VirtualHost 192.168.0.1:443>
DocumentRoot /var/www/html
ServerName www.yourdomain.com
SSLEngine on
SSLCertificateFile /path/to/your_domain_name.crt
SSLCertificateKeyFile /path/to/your_private.key
SSLCertificateChainFile /etc/pki/tls/certs/example_com.ca-bundle
</VirtualHost>
```

> SSLCertificateFile should be your Comodo certificate file (eg. your_domain_name.crt).
>
> SSLCertificateKeyFile should be the key file generated when you created the CSR.
>
> SSLCertificateChainFile should be the Comodo intermediate certificate file (ComodoRSACA.crt)





### redirect http to https

```
NameVirtualHost *:80
<VirtualHost *:80>
   ServerName mysite.example.com
   DocumentRoot /usr/local/apache2/htdocs 
   Redirect permanent / https://mysite.example.com/
</VirtualHost>

<VirtualHost _default_:443>
   ServerName mysite.example.com
  DocumentRoot /usr/local/apache2/htdocs
  SSLEngine On
 # etc...
</VirtualHost>
```

