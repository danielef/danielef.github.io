---
layout: post
title: Install Docker on CentOS 7
---

### Intro

Docker requires a 64-bit installation regardless of your CentOS version. Also, your kernel must be 3.10 at minimum, which CentOS 7 runs.
{% highlight bash %}
$ uname -r
3.10.0-229.el7.x86_64
{% endhighlight %}

### Installing

Make sure your existing yum packages are up-to-date.
{% highlight bash %}
# yum update
{% endhighlight %}

Add the yum repository file
{% highlight bash %}
$ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF
{% endhighlight %}

Install the Docker package
{% highlight bash %}
# yum install docker-engine
{% endhighlight %}

Start the Docker daemon
{% highlight bash %}
# service docker start
{% endhighlight %}

To ensure Docker starts when you boot your system, do the following:
{% highlight bash %}
# systemctl enable docker.service
{% endhighlight %}
