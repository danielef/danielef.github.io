---
layout: post
title: Install Postgis 2.1 on Centos 7
---

### Adding pgdg94 Repository 

By default, Centos 7 includes Postgresql 9.2 and Postgis 2.0. To install a most recently version we must to add the PostgreSQL official RPM Repository to YUM system.

**Note** *For avoid a dependency hell, uninstall your Postgis and PostgreSQL previously installed*

{% highlight bash %}
# yum erase postgresql-server postgis
# yum autoremove
{% endhighlight %}

Download and install latest RPM Repository file for Centos 7 as follow: 

{% highlight bash %}
# cd /opt/
# wget http://yum.postgresql.org/9.4/redhat/rhel-7-x86_64/pgdg-centos94-9.4-1.noarch.rpm
# rpm -ivh pgdg-centos94-9.4-1.noarch.rpm 
{% endhighlight %}

### Installing

After this, you can install Postgis 2.1.8 and PostgreSQL 9.4.4

{% highlight bash %}
# yum install postgresql94-server postgis2_94
{% endhighlight %}

### First Start

Activate PortgreSQL to start automatically on boot

{% highlight bash %}
# systemctl enable postgresql-9.4
{% endhighlight %}

Initialize PostgreSQL database using following command
{% highlight bash %}
# /usr/pgsql-9.4/bin/postgresql94-setup initdb
{% endhighlight %}

Finally, start PostgreSQL
{% highlight bash %}
# systemctl start postgresql-9.4
{% endhighlight %}

