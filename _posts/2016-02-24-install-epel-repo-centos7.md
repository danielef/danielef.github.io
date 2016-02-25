---
layout: post
title: Install EPEL Repository on CentOS 7
---


### Intro

The EPEL repository provides useful software packages that are not included in the official CentOS or Red Hat Enterprise Linux repositories.

### CentOS Extras repository

To install the EPEL package, run the following command:

{% highlight bash %}
# sudo yum install epel-release
{% endhighlight %}

 **Note:** *If that command doesn’t work, perhaps because the CentOS Extras repository is disabled. Try to download EPEL RPM manually:*

{% highlight bash %}
# wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
# sudo rpm -Uvh epel-release-7*.rpm
{% endhighlight %}
