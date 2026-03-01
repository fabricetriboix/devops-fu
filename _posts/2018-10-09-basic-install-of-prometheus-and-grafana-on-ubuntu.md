---
layout: post
title: How to do a basic install of Prometheus and Grafana on Ubuntu?
date: 2018-10-09
categories: [sysadmin]
tags: [prometheus, grafana]
---

Machine requirements
====================

I will assume your machine is Ubuntu >= 16.04.

Please make sure only ports 80 and 443 are open.

I assume there are two DNS entries to your machine, such as
`prometheus.blablabla.com` and `grafana.blablabla.com` (replace as
appropriate throughout the tutorial).

Install Prometheus
==================

Follow the steps (you can replace version 2.4.3 with whatever latest
version is available for you):

{% highlight shell %}
$ sudo useradd --no-create-home --shell /bin/false prometheus
$ wget https://github.com/prometheus/prometheus/releases/download/v2.4.3/prometheus-2.4.3.linux-amd64.tar.gz
$ tar xf prometheus-2.4.3.linux-amd64.tar.gz
$ cd prometheus-2.4.3.linux-amd64
$ sudo cp prometheus promtool /usr/local/bin
$ sudo mkdir /etc/prometheus /var/lib/prometheus
$ sudo cp -r prometheus.yml consoles console_libraries /etc/prometheus
$ sudo chown prometheus.prometheus /var/lib/prometheus
{% endhighlight %}

Create the file `/etc/systemd/system/prometheus.service` and put the
following content in it:

{% highlight ini %}
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus \
    --web.console.templates /etc/prometheus/consoles \
    --web.console.libraries /etc/prometheus/console_libraries
Restart=always

[Install]
WantedBy=multi-user.target
{% endhighlight %}

Then run:

{% highlight shell %}
$ sudo systemctl daemon-reload
$ sudo systemctl enable prometheus
$ sudo systemctl start prometheus
{% endhighlight %}

Install Grafana
===============

To install Grafana:

{% highlight shell %}
$ echo "deb https://packagecloud.io/grafana/stable/debian/ stretch main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
$ curl -sSL https://packagecloud.io/gpg.key | sudo apt-key add -
$ sudo apt update
$ sudo apt install grafana
$ sudo systemctl daemon-reload
$ sudo systemctl enable grafana-server
$ sudo systemctl start grafana-server
{% endhighlight %}

**Do not try to configure Grafana yet! We need to do all the SSL stuff
first.**

Reverse-proxy and SSL certificates
==================================

Install certbot
---------------

Follow the steps:

{% highlight shell %}
$ sudo apt install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt update
$ sudo apt install certbot python-certbot-nginx
{% endhighlight %}

Reverse-proxy for Prometheus
----------------------------

Prometheus web interface has no authentication mechanism, so we will
add one with nginx.

To install nginx as a reverse-proxy for SSL termination and
authentication for Prometheus (replace USERNAME and PASSWORD with a
username and password of your choice):

{% highlight shell %}
$ sudo apt install nginx
$ sudo rm /etc/nginx/sites-enabled/default
$ echo "USERNAME:$(openssl passwd -apr1 PASSWORD)" | tee -a /etc/nginx/prometheus.htpasswd
{% endhighlight %}

Then edit the file `/etc/nginx/sites-available/prometheus` to look
like this (replace `prometheus.blablabla.com` with your real domain
name; NB: don’t worry if the SSL config seems to lack some stuff,
things will be added by certbot later):

{% highlight nginx %}
server {
  listen 80;
  server_name prometheus.blablabla.com;
}

server {
  listen 443 ssl;
  server_name prometheus.blablabla.com;

  location / {
    proxy_pass http://localhost:9090;
    proxy_set_header Host $host;
    auth_basic "My Prometheus";
    auth_basic_user_file /etc/nginx/prometheus.htpasswd;
  }
}
{% endhighlight %}

Reload nginx:

{% highlight shell %}
$ sudo ln -s /etc/nginx/sites-available/prometheus /etc/nginx/sites-enabled/
$ sudo systemctl reload nginx
{% endhighlight %}

Create an SSL certificate for `prometheus.blablabla.com`:

{% highlight shell %}
$ sudo certbot --nginx -d prometheus.blablabla.com
{% endhighlight %}

Certbot will start an interactive session. Answer as follows:

 - When asked for your email address, enter it
 - Agree to the terms of service
 - Agree or not to the marketing, that has no incidence on your SSL
   certificate
 - When asked to redirect all HTTP traffic to HTTPS, choose to
   redirect

Reverse-proxy for Grafana
-------------------------

Edit the file `/etc/nginx/sites-available/grafana` to look like this
(same remarks as before):

{% highlight nginx %}
server {
  listen 80;
  server_name grafana.blablabla.com;
}

server {
  listen 443 ssl;
  server_name grafana.blablabla.com;

  location / {
    proxy_pass http://localhost:3000;
    proxy_set_header Host $host;
  }
}
{% endhighlight %}

Reload nginx:

{% highlight shell %}
$ sudo certbot --nginx -d grafana.blablabla.com
{% endhighlight %}

Answer the interactive prompts in the same manner as previously.

Configure Grafana
-----------------

Open your favourite browser to `https://grafana.blablabla.com`. The
Grafana login page should come up. Enter admin for both the username
and password; the next page should ask you for a more secure password.

Then click on the 2nd icon "Add data source" and enter the following
details:

 - Name: Whatever you like
 - Type: Prometheus
 - URL: `http://localhost:9090` (reminder: Prometheus is running on
   the same VM as Grafana)
 - Leave the rest untouched
 - Click "Save & Test"
 - You should see a message at the bottom of the page "Data source is
   working"

Install and configure Alertmanager
==================================

This is an optional extra to receive some notifications when certain
metrics go outside a certain range.

Follow these steps to install Alertmanager (you can replace version
0.15.2 with whatever latest version is available for you:

{% highlight shell %}
$ sudo useradd --no-create-home --shell /bin/false alertmanager
$ wget //github.com/prometheus/alertmanager/releases/download/v0.15.2/alertmanager-0.15.2.linux-amd64.tar.gz
$ tar xf alertmanager-0.15.2.linux-amd64.tar.gz
$ cd alertmanager-0.15.2.linux-amd64
$ sudo cp alertmanager amtool /usr/local/bin
$ sudo mkdir -p /etc/alertmanager/templates /var/lib/alertmanager
$ sudo chown alertmanager.alertmanager /var/lib/alertmanager
{% endhighlight %}

Create the file `/etc/alertmanager/alertmanager.yml` so it looks like
this (substitute the correct values for your setup):

{% highlight yaml %}
global:
  smtp_smarthost: localhost:25
  smtp_from: alertmanager@prometheus.blablabla.com
  smtp_auth_username: ""
  smtp_auth_password: ""
  smtp_require_tls: false

templates:
- /etc/alertmanager/templates/*.tmpl

route:
  repeat_interval: 1h
  receiver: MYTEAM

receivers:
- name: MYTEAM
  email_configs:
    - to: MYTEAM@MYORG.com
  slack_configs:
    - api_url: https://hooks.slack.com/services/XXXXXX/XXXXXX/XXXXXX
      send_resolved: true
{% endhighlight %}

Create the file `/etc/systemd/system/altermanager.service` and put the
following content in it:

{% highlight ini %}
[Unit]
Description=Alertmanager
Wants=network-online.target
After=network-online.target

[Service]
User=alertmanager
Group=alertmanager
Type=simple
ExecStart=/usr/local/bin/alertmanager \
        --config.file /etc/alertmanager/alertmanager.yml \
        --storage.path /var/lib/alertmanager
Restart=always

[Install]
WantedBy=multi-user.target
{% endhighlight %}

Then run:

{% highlight shell %}
$ sudo systemctl daemon-reload
$ sudo systemctl enable alertmanager
$ sudo systemctl start alertmanager
{% endhighlight %}

You need to tell prometheus that an alertmanager service is available.
To do so, edit the `/etc/prometheus/prometheus.yml` file and add the
following at the end:

{% highlight yaml %}
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - localhost: 9093
{% endhighlight %}

Then:

{% highlight shell %}
$ sudo systemctl restart prometheus
{% endhighlight %}

You should now have a basic Prometheus setup working.
