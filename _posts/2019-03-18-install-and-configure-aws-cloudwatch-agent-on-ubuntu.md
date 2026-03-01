---
layout: post
title: How to install and configure AWS CloudWatch agent on Ubuntu?
date: 2019-03-18
categories: [aws]
tags: [cloudwatch]
---

I will assume that you want to install the CloudWatch agent on an EC2
instance (as opposed to an on-premise server). I took the example of
sending Apache logs to CloudWatch Logs.

First, make sure your EC2 instance has an IAM role attached with the
`CloudWatchAgentServerPolicy` policy.

SSH to your EC2 instance running Ubuntu 18.04 and running the
following:

{% highlight shell %}
$ wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
$ sudo dpkg -i amazon-cloudwatch-agent.deb
{% endhighlight %}

NB: Do not modify any installed files, especially the ones in
`/opt/aws/amazon-cloudwatch-agent/etc`.

If you run Amazon Linux rather then Ubuntu, the download link is:

{% highlight shell %}
$ wget https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm
{% endhighlight %}

You can run the configuration wizard
`/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard`,
or edit the configuration file manually which gives you more control.
The documentation is available
[here](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html).

For example:

{% highlight shell %}
$ sudo -e /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
{% endhighlight %}

An example for me (comments added, JSON does not support comments):

{% highlight json %}
{
  "agent": {
    "metrics_collection_interval": 60,  // Default metrics collection interval in seconds
    "logfile": "/var/log/amazon-cloudwatch-agent.log",  // Where should the CloudWatch Agent writes its logs
    "debug": false  // Should CloudWatch Agent run in debug mode
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/apache2/access.log",  // File where logs are written that should be forwarded to CloudWatch Logs
            "log_group_name": "apache-access",  // Log group to save logs to
            "log_stream_name": "{instance_id}"  // Log stream to save logs to
          },
          {
            "file_path": "/var/log/apache2/error.log",
            "log_group_name": "apache-error",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  },
  "metrics": {
    "append_dimensions": {  // Dimensions to add to all metrics
      "AutoScalingGroupName": "${aws:AutoScalingGroupName}",
      "InstanceId": "${aws:InstanceId}"
    },
    "aggregation_dimensions": [["AutoScalingGroupName"]],  // Dimensions on which metrics are to be aggregated
    "metrics_collected": {
      "collectd": {  // Collect metrics through collectd; you must install collectd for this to work
        "metrics_aggregation_interval": 60
      },
      "statsd": {  // CloudAgent will act as a statsd server to allow collection of additional metrics
        "metrics_aggregation_interval": 60,
        "metrics_collection_interval": 10,
        "service_address": ":8125"
      },
      "cpu": {
        "measurement": [
          "cpu_usage_idle",
          "cpu_usage_iowait",
          "cpu_usage_user",
          "cpu_usage_system"
        ],
        "totalcpu": true  // Set to `false` to show each CPU, `true` to aggregate all CPUs
      },
      "disk": {
        "measurement": [
          "used_percent",
          "inodes_free"
        ],
        "resources": [
          "*"
        ]
      },
      "diskio": {
        "measurement": [
          "io_time",
          "write_bytes",
          "read_bytes",
          "writes",
          "reads"
        ],
        "resources": [
          "*"
        ]
      },
      "mem": {
        "measurement": [
          "mem_used_percent"
        ]
      },
      "netstat": {
        "measurement": [
          "tcp_established",
          "tcp_time_wait"
        ]
      },
      "swap": {
        "measurement": [
          "swap_used_percent"
        ]
      }
    }
  }
}
{% endhighlight %}

To start the CloudWatch agent, run the following:

{% highlight shell %}
$ sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json -s
{% endhighlight %}

Please note this will convert the JSON config file into a TOML file
which is actually used by the CloudWatch Agent; the original JSON file
will be deleted.

The CloudWatch agent is integrated with systemd. It will start
automatically after a reboot, and you can restart it like so:

{% highlight shell %}
$ sudo systemctl restart amazon-cloudwatch-agent
{% endhighlight %}

Restarting the agent using systemd will use the existing TOML file and
will ignore any new JSON file. If you create a new configuration JSON
file, you will need to use the `amazon-cloudwatch-agent-ctl` command.
