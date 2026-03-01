---
layout: post
title: How to properly use certbot with Nginx on Ubuntu?
date: 2018-12-18
categories: [ssl]
tags: [certbot, letsencrypt]
---

Install and configure NGINX:

{% highlight shell %}
$ sudo apt update
$ sudo apt install nginx
$ sudo rm /etc/nginx/sites-enabled/default
$ sudo vi /etc/nginx/sites-available/MYSITE  # See below
$ sudo ln -s /etc/nginx/sites-available/MYSITE /etc/nginx/sites-enabled
$ sudo systemctl restart nginx
{% endhighlight %}

The `/etc/nginx/sites-available/MYSITE` config file should look like
so:

{% highlight nginx %}
server {
  listen 80;
  server_name MYSITE.COM WWW.MYSITE.COM;
}

server {
  listen 443 ssl;
  server_name MYSITE.COM WWW.MYSITE.COM;
  server_tokens off;

  # If you use nginx as reverse-proxy
  location / {
    proxy_pass http://localhost:8080;
    proxy_set_header Host $host;
  }
}
{% endhighlight %}

Install certbot:

{% highlight shell %}
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt update
$ sudo apt install certbot python-certbot-nginx
{% endhighlight %}

Create the certificate:

{% highlight shell %}
$ sudo certbot --nginx --agree-tos --redirect --uir --hsts --staple-ocsp --email ME@MYSITE.COM -d MYSITE.COM -d WWW.MYSITE.COM
{% endhighlight %}

You could potentially use the `--must-staple` argument to enable the
OCSP "must staple" extension, but it doesn't work well with Firefox
(to fix in your Firefox browser: go to [about:config](about:config),
and set `security.ssl.enable_ocsp_must_staple` to `false`).

The arguments are:

 - `--nginx`: Use NGINX for authentication and install certificate for NGINX
 - `--agree-tos`: Agree with Let's Encrypt terms of service
 - `--redirect`: Add NGINX config to redirect HTTP to HTTPS
 - `--uir`: Add `Content-Security-Policy: upgrade-insecure-requests`
   to HTTP responses
 - `--hsts`: Add `Strict-Transport-Security` header to HTTP responses
 - `--staple-ocsp`: Enable OSCP stapling (allow browser to skip
   verification of whether the SSL certificate has been revoked or
   not)
 - `--email`: Email address to use to register with Let's Encrypt, and
   potentially for recovery

