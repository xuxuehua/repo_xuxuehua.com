---
title: "github_pages"
date: 2018-09-06 17:46
---


[TOC]


# github_pages



## Reference 

https://help.github.com/articles/troubleshooting-custom-domains/



## A records 

185.199.108.153

185.199.109.153

185.199.110.153

185.199.111.153

- You may see a different IP address, since we serve Pages with a global [Content Delivery Network](http://en.wikipedia.org/wiki/Content_delivery_network). Use `dig username.github.io` to see the full resolution path. Note that DNS caching may cause a delay.



## Over HTTPS

- If you're using an `A` record that points to `192.30.252.153` or `192.30.252.154`, you'll need to update your DNS settings for your site to be available over HTTPS or served with a [Content Delivery Network](http://en.wikipedia.org/wiki/Content_delivery_network). For more information, see "[HTTPS errors](https://help.github.com/articles/troubleshooting-custom-domains/#https-errors)."



## Discontinue 

- If you're using an `A` record that points to `207.97.227.245` or `204.232.175.78`, you'll need to update your DNS settings, as we no longer serve Pages directly from those servers.



##CNAME

If you configured `ALIAS`, `ANAME`, or `CNAME` records through your DNS provider, then your DNS record should point to your GitHub Pages default domain, such as `YOUR-GITHUB-USERNAME.github.io`. Your default GitHub Pages domain is determined by the type of pages site you have. For examples, see this [domain chart](https://help.github.com/articles/custom-domain-redirects-for-github-pages-sites).

If you need help creating these records, contact your DNS provider as they should have the most detailed instructions. For some guidelines on configuring a DNS record with DNS providers, see "[Using a custom domain with GitHub Pages](https://help.github.com/articles/using-a-custom-domain-with-github-pages/)."