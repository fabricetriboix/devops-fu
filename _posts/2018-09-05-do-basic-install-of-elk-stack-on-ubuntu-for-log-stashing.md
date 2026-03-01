---
layout: post
title: How to do a basic install of an ELK stack on Ubuntu for log stashing?
date: 2018-09-05
categories: [sysadmin]
tags: [elk]
---

Use a machine with at least 4GiB of RAM. This guide will install
ElasticSearch, Logstash and Kibana all on the same machine, so this is
suited only for a small-scale setup.

**Warning**: ELK is under contant development, and this guide is
probably out of date now...

Install and configure ElasticSearch
===================================

{% highlight shell %}
$ sudo apt install apt-transport-https software-properties-common wget
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt update
$ sudo apt install oracle-java8-installer
$ wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
$ echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
$ sudo apt update
$ sudo apt install elasticsearch
$ sudo systemctl enable elasticsearch
$ sudo systemctl start elasticsearch
{% endhighlight %}

Then edit `/etc/elasticsearch/elasticsearch.yml` and do the following:

 - Set `node.name` to something descriptive
 - Set `cluster.name` to something unique to avoid issues with
   auto-discovery
 - Set `network.host` to "localhost"
 - Change `path.data` and `path.logs` if necessary

Edit `/etc/elasticsearch/jvm.options` and set the heap size according
to your machine RAM, for example (to set it to 2GiB):

{% highlight conf %}
-Xms2g
-Xmx2g
{% endhighlight %}

Probably the easiest is to divide the available RAM by 4 and take that
figure for each service (ElasticSearch, Logstash and Kibana, with the
rest for the OS).

A setting of 1GiB is probably the minimum you should use.

**Make sure you disable swapping!**

Install and configure Kibana
============================

Run this: `$ sudo apt install kibana`. Then edit
`/etc/kibana/kibana.yml`
and set `server.host` to "localhost". Then:

{% highlight shell %}
$ sudo systemctl enable kibana
$ sudo systemctl start kibana
{% endhighlight %}

Create a DNS entry for your kibana host and an SSL certificate.

Install nginx as reverse proxy with authentication:

{% highlight shell %}
$ sudo apt install nginx
$ sudo rm /etc/nginx/sites-enabled/default
$ echo "admin:$(openssl passwd -apr1 PASSWORD)" | sudo tee -a /etc/nginx/kibana.htpasswd
{% endhighlight %}

Then edit an nginx site file (eg: `/etc/nginx/sites-available/kibana`)
and make it look like this:

{% highlight nginx %}
server {
    listen 80 default_server;
    server_name _;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 default_server ssl http2;
    server_name _;

    ssl_certificate /etc/letsencrypt/live/YOURDOMAIN/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/YOURDOMAIN/privkey.pem;

    auth_basic "My Kibana";
    auth_basic_user_file /etc/nginx/kibana.htpasswd;

    location / {
        proxy_pass http://localhost:5601;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
{% endhighlight %}

Activate the site like so:

{% highlight shell %}
$ sudo ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/kibana
$ sudo nginx -t
$ sudo systemctl reload nginx
{% endhighlight %}

Install and configure logstash
==============================

Just run: `$ sudo apt install logstash`. Edit
`/etc/logstash/jvm.options` and set the heap size according to your
machine RAM, for example (to set it to 2GiB):

{% highlight conf %}
-Xms2g
-Xmx2g
{% endhighlight %}

A setting of 1GiB is probably the minimum you should use. Create a
file `/etc/logstash/conf.d/GIVE_ME_A_NAME.conf` and edit it so it
looks like this:

{% highlight conf %}
input {
    beats {
        port => "5044"
    }
}

filter {
    if [fields][log_type] == "apache-access" {
        grok {
            match => { "message" => "%{IPORHOST:vhost}:%{NUMBER:vhost_port} %{COMBINEDAPACHELOG}" }
        }
        geoip {
            source => "clientip"
        }
    } else if [fields][log_type] == "apache-error" {
       grok {
           match => { "message" => "%{IPORHOST:vhost} \[%{TIMESTAMP_ISO8601:timestamp}\] \[%{DATA:module}:%{LOGLEVEL}\] \[pid: %{POSINT:pid}:tid %{DATA:tid}\] \[OS error: %{DATA:oserror}\] \[client %{DATA:clientip}\] %{GREEDYDATA:error_message}" }
       }
       geoip {
           source => "clientip"
       }
    }
}

output {
    elasticsearch {
        hosts => [ "localhost:9200" ]
        index => "%{[fields][log_type]}-%{+YYYYMMdd}"
    }
}
{% endhighlight %}

Then run:

{% highlight shell %}
$ sudo systemctl restart logstash
{% endhighlight %}

NB: The grok patterns assume the following log formats for apache:

{% highlight apache %}
{% raw %}
ErrorLogFormat "%-v [%{cu}t] [%-m:%l] [pid: %-P:tid %-T] [OS error: %-E] [client %-a] %M"
LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
{% endraw %}
{% endhighlight %}

NB: You can use [this](https://grokdebug.herokuapp.com/) to test your
grok patterns.

Install and configure filebeat
==============================

Filebeat goes on the machine where your service is running, not the
ELK machine.

To install filebeat:

{% highlight shell %}
$ wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
$ sudo apt install apt-transport-https
$ echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
$ sudo apt update
$ sudo apt install filebeat
{% endhighlight %}

Then edit `/etc/filebeat/filebeat.yml` so it looks like this:

{% highlight yaml %}
filebeat.inputs:
- type: log
  paths:
  - /var/log/apache2/access.log
  fields:
    log_type: apache-access

- type: log
  paths:
  - /var/log/apache2/error.log
  fields:
    log_type: apache-error

output.logstash:
  hosts: ["ELK_HOSTNAME_OR_IP:5044"]

output.console:
  pretty: true
{% endhighlight %}

Then run:

{% highlight shell %}
$ sudo systemctl restart filebeat
{% endhighlight %}

You should have a working system now.
