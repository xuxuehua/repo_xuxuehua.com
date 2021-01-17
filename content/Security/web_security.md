---
title: "web_security"
date: 2020-12-21 08:55
---
[toc]







# Web Security Cheat Sheet



| Guideline                                                    | Security Benefit | Implementation Difficulty | Order† | Requirements                                                 | Notes                                                        |
| ------------------------------------------------------------ | ---------------- | ------------------------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [HTTPS](https://infosec.mozilla.org/guidelines/web_security#https) | **MAXIMUM**      | **MEDIUM**                |        | Mandatory                                                    | Sites should use HTTPS (or other secure protocols) for all communications |
| [Public Key Pinning](https://infosec.mozilla.org/guidelines/web_security#http-public-key-pinning) | **LOW**          | **MAXIMUM**               | --     | Mandatory for maximum risk sites only                        | Not recommended for most sites                               |
| [Redirections from HTTP](https://infosec.mozilla.org/guidelines/web_security#http-redirections) | **MAXIMUM**      | **LOW**                   | 3      | Mandatory                                                    | Websites must redirect to HTTPS, API endpoints should disable HTTP entirely |
| [Resource Loading](https://infosec.mozilla.org/guidelines/web_security#resource-loading) | **MAXIMUM**      | **LOW**                   | 2      | Mandatory for all websites                                   | Both passive and active resources should be loaded through protocols using TLS, such as HTTPS |
| [Strict Transport Security](https://infosec.mozilla.org/guidelines/web_security#http-strict-transport-security) | **HIGH**         | **LOW**                   | 4      | Mandatory for all websites                                   | Minimum allowed time period of six months                    |
| [TLS Configuration](https://infosec.mozilla.org/guidelines/web_security#https) | **MEDIUM**       | **MEDIUM**                | 1      | Mandatory                                                    | Use the most secure Mozilla TLS configuration for your user base, typically [Intermediate](https://wiki.mozilla.org/Security/Server_Side_TLS#Intermediate_compatibility_.28default.29) |
| [Content Security Policy](https://infosec.mozilla.org/guidelines/web_security#content-security-policy) | **HIGH**         | **HIGH**                  | 10     | Mandatory for new websites Recommended for existing websites | Disabling inline script is the greatest concern for CSP implementation |
| [Cookies](https://infosec.mozilla.org/guidelines/web_security#cookies) | **HIGH**         | **MEDIUM**                | 7      | Mandatory for all new websites Recommended for existing websites | All cookies must be set with the Secure flag, and set as restrictively as possible |
| [contribute.json](https://infosec.mozilla.org/guidelines/web_security#contributejson) | **LOW**          | **LOW**                   | 9      | Mandatory for all new Mozilla websites Recommended for existing Mozilla sites | Mozilla sites should serve contribute.json and keep contact information up-to-date |
| [Cross-origin Resource Sharing](https://infosec.mozilla.org/guidelines/web_security#cross-origin-resource-sharing) | **HIGH**         | **LOW**                   | 11     | Mandatory                                                    | Origin sharing headers and files should not be present, except for specific use cases |
| [Cross-site Request Forgery Tokenization](https://infosec.mozilla.org/guidelines/web_security#csrf-prevention) | **HIGH**         | **UNKNOWN**               | 6      | Varies                                                       | Mandatory for websites that allow destructive changes Unnecessary for all other websites Most application frameworks have built-in CSRF tokenization to ease implementation |
| [Referrer Policy](https://infosec.mozilla.org/guidelines/web_security#referrer-policy) | **LOW**          | **LOW**                   | 12     | Recommended for all websites                                 | Improves privacy for users, prevents the leaking of internal URLs via `Referer` header |
| [robots.txt](https://infosec.mozilla.org/guidelines/web_security#robotstxt) | **LOW**          | **LOW**                   | 14     | Optional                                                     | Websites that implement robots.txt must use it only for noted purposes |
| [Subresource Integrity](https://infosec.mozilla.org/guidelines/web_security#subresource-integrity) | **MEDIUM**       | **MEDIUM**                | 15     | Recommended‡                                                 | ‡ Only for websites that load JavaScript or stylesheets from foreign origins |
| [X-Content-Type-Options](https://infosec.mozilla.org/guidelines/web_security#x-content-type-options) | **LOW**          | **LOW**                   | 8      | Recommended for all websites                                 | Websites should verify that they are setting the proper MIME types for all resources |
| [X-Frame-Options](https://infosec.mozilla.org/guidelines/web_security#x-frame-options) | **HIGH**         | **LOW**                   | 5      | Mandatory for all websites                                   | Websites that don't use DENY or SAMEORIGIN must employ clickjacking defenses |
| [X-XSS-Protection](https://infosec.mozilla.org/guidelines/web_security#x-xss-protection) | **LOW**          | **MEDIUM**                | 13     | Mandatory for all new websites Recommended for existing websites | Manual testing should be done for existing websites, prior to implementation |





# HTTP Strict Transport Security

HTTP Strict Transport Security (HSTS) is an HTTP header that notifies user agents to only connect to a given site over HTTPS, even if the scheme chosen was HTTP. Browsers that have had HSTS set for a given site will transparently upgrade all requests to HTTPS. HSTS also tells the browser to treat TLS and certificate-related errors more strictly by disabling the ability for users to bypass the error page.

The header consists of one mandatory parameter (`max-age`) and two optional parameters (`includeSubDomains` and `preload`), separated by semicolons.



## Directives

- `max-age:` how long user agents will redirect to HTTPS, in seconds
- `includeSubDomains:` whether user agents should upgrade requests on subdomains
- `preload:` whether the site should be included in the [HSTS preload list](https://hstspreload.org/)



### `max-age`

Must be set to a minimum of six months (15768000), but longer periods such as two years (63072000) are recommended. Note that once this value is set, the site must continue to support HTTPS until the expiry time has been reached.



### `includeSubDomains` 

Notifies the browser that all subdomains of the current origin should also be upgraded via HSTS. 

For example, setting `includeSubDomains` on `domain.mozilla.com` will also set it on `host1.domain.mozilla.com` and `host2.domain.mozilla.com`. Extreme care is needed when setting the `includeSubDomains` flag, as it could disable sites on subdomains that don’t yet have HTTPS enabled.



### `preload` 

Allows the website to be included in the [HSTS preload list](https://hstspreload.org/), upon submission. As a result, web browsers will do HTTPS upgrades to the site without ever having to receive the initial HSTS header. This prevents downgrade attacks upon first use and is recommended for all high risk websites. Note that being included in the HSTS preload list requires that `includeSubDomains` also be set.



* Examples

```
# Only connect to this site via HTTPS for the two years (recommended)
Strict-Transport-Security: max-age=63072000

# Only connect to this site and subdomains via HTTPS for the next two years and also include in the preload list
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
```





# HTTP Redirections

Websites may continue to listen on port 80 (HTTP) so that users do not get connection errors when typing a URL into their address bar, as browsers currently connect via HTTP for their initial request. Sites that listen on port 80 should only redirect to the same resource on HTTPS. Once the redirection has occurred, [HSTS](https://infosec.mozilla.org/guidelines/web_security#http-strict-transport-security) should ensure that all future attempts go to the site via HTTP are instead sent directly to the secure site. APIs or websites not intended for public consumption should disable the use of HTTP entirely.



## 301

Redirections should be done with the 301 redirects, unless they redirect to a different path, in which case they may be done with 302 redirections. Sites should avoid redirections from HTTP to HTTPS on a different host, as this prevents HSTS from being set.



```
# Redirect all incoming http requests to the same site and URI on https, using nginx
server {
  listen 80;

  return 301 https://$host$request_uri;
}

# Redirect for site.mozilla.org from http to https, using Apache
<VirtualHost *:80>
  ServerName site.mozilla.org
  Redirect permanent / https://site.mozilla.org/
</VirtualHost>
```



# HTTP Public Key Pinning

[Maximum risk](https://infosec.mozilla.org/guidelines/risk/standard_levels#standard-risk-levels-definition-and-nomenclature) sites must enable the use of HTTP Public Key Pinning (HPKP). HPKP instructs a user agent to bind a site to specific root certificate authority, intermediate certificate authority, or end-entity public key. This prevents certificate authorities from issuing unauthorized certificates for a given domain that would nevertheless be trusted by the browsers. These fraudulent certificates would allow an active attacker to MitM and impersonate a website, intercepting credentials and other sensitive data.



HPKP must be implemented with extreme care. This includes having backup key pins, testing on a non-production domain, testing with `Public-Key-Pins-Report-Only` and then finally doing initial testing with a very short-lived `max-age` directive.



## Directives

- `max-age:` number of seconds the user agent will enforce the key pins and require a site to use a cert that satisfies them
- `includeSubDomains:` whether user agents should pin all subdomains to the same pins

Unlike with HSTS, what to set `max-age` is highly individualized to a given site. A longer value is more secure, but screwing up your key pins will result in your site being unavailable for a longer period of time. Recommended values fall between 15 and 120 days.



```
# Pin to DigiCert, Let's Encrypt, and the local public-key, including subdomains, for 15 days
Public-Key-Pins: max-age=1296000; includeSubDomains; pin-sha256="WoiWRyIOVNa9ihaBciRSC7XHjliYS9VwUGOIud4PB18=";
 pin-sha256="YLh1dUR9y6Kja30RrAn7JKnbQG/uEtLMkBgFF2Fuihg="; pin-sha256="P0NdsLTMT6LSwXLuSEHNlvg4WxtWb5rIJhfZMyeXUE0="
```



# Resource Loading

All resources — whether on the same origin or not — should be loaded over secure channels. Secure (HTTPS) websites that attempt to load active resources such as JavaScript insecurely will be blocked by browsers. As a result, users will experience degraded UIs and “mixed content” warnings. Attempts to load passive content (such as images) insecurely, although less risky, will still lead to degraded UIs and can allow active attackers to deface websites or phish users.



```
<!-- HTTPS is a fantastic way to load a JavaScript resource -->
<script src="https://code.jquery.com/jquery-1.12.0.min.js"></script>

<!-- Attempts to load over HTTP will be blocked and will generate mixed content warnings -->
<script src="http://code.jquery.com/jquery-1.12.0.min.js"></script>

<!-- Although passive content won't be blocked, it will still generate mixed content warnings -->
<img src="http://very.badssl.com/image.jpg">
```





# Content Security Policy

Content Security Policy (CSP) is an HTTP header that allows site operators fine-grained control over where resources on their site can be loaded from. The use of this header is the best method to prevent cross-site scripting (XSS) vulnerabilities. Due to the difficulty in retrofitting CSP into existing websites, CSP is mandatory for all new websites and is strongly recommended for all existing high-risk sites.

The primary benefit of CSP comes from disabling the use of unsafe inline JavaScript. Inline JavaScript – either reflected or stored – means that improperly escaped user-inputs can generate code that is interpreted by the web browser as JavaScript. By using CSP to disable inline JavaScript, you can effectively eliminate almost all XSS attacks against your site.

Note that disabling inline JavaScript means that *all* JavaScript must be loaded from `<script>` src tags . Event handlers such as *onclick* used directly on a tag will fail to work, as will JavaScript inside `<script>` tags but not loaded via `src`. Furthermore, inline stylesheets using either `<style>` tags or the `style` attribute will also fail to load. As such, care must be taken when designing sites so that CSP becomes easier to implement.

## Implementation Notes

- Aiming for `default-src https:` is a great first goal, as it disables inline code and requires https.
- For existing websites with large codebases that would require too much work to disable inline scripts, `default-src https: 'unsafe-inline'` is still helpful, as it keeps resources from being accidentally loaded over http. However, it does not provide any XSS protection.
- It is recommended to start with a reasonably locked down policy such as `default-src 'none'; img-src 'self'; script-src 'self'; style-src 'self'` and then add in sources as revealed during testing.
- In lieu of the preferred HTTP header, pages can instead include a `<meta http-equiv=`“`Content-Security-Policy`”` content=`“`…`”`>` tag. If they do, it should be the first `<meta>` tag that appears inside `<head>`.
- Care needs to be taken with `data:` URIs, as these are unsafe inside `script-src` and `object-src` (or inherited from `default-src`).
- Similarly, the use of `script-src 'self'` can be unsafe for sites with JSONP endpoints. These sites should use a `script-src` that includes the path to their JavaScript source folder(s).
- Unless sites need the ability to execute plugins such as Flash or Silverlight, they should disable their execution with `object-src 'none'`.
- Sites should ideally use the `report-uri` directive, which POSTs JSON reports about CSP violations that do occur. This allows CSP violations to be caught and repaired quickly.
- Prior to implementation, it is recommended to use the `Content-Security-Policy-Report-Only` HTTP header, to see if any violations would have occurred with that policy.



```
# Disable unsafe inline/eval, only allow loading of resources (images, fonts, scripts, etc.) over https
# Note that this does not provide any XSS protection
Content-Security-Policy: default-src https:


<!-- Do the same thing, but with a <meta> tag -->
<meta http-equiv="Content-Security-Policy" content="default-src https:">


# Disable the use of unsafe inline/eval, allow everything else except plugin execution
Content-Security-Policy: default-src *; object-src 'none'


# Disable unsafe inline/eval, only load resources from same origin except also allow images from imgur
# Also disables the execution of plugins
Content-Security-Policy: default-src 'self'; img-src 'self' https://i.imgur.com; object-src 'none'


# Disable unsafe inline/eval and plugins, only load scripts and stylesheets from same origin, fonts from google,
# and images from same origin and imgur. Sites should aim for policies like this.
Content-Security-Policy: default-src 'none'; font-src https://fonts.gstatic.com;
			 img-src 'self' https://i.imgur.com; object-src 'none'; script-src 'self'; style-src 'self'


# Pre-existing site that uses too much inline code to fix
# but wants to ensure resources are loaded only over https and disable plugins
Content-Security-Policy: default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'


# Don't implement the above policy yet; instead just report violations that would have occurred
Content-Security-Policy-Report-Only: default-src https:; report-uri /csp-violation-report-endpoint/


# Disable the loading of any resources and disable framing, recommended for APIs to use
Content-Security-Policy: default-src 'none'; frame-ancestors 'none'
```



