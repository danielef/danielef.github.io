---
layout: post
title: Install Hamachi on CentOS 7
---


### Downloading and Install Latest Hamachi

Download latest Logmein Hamachi release from its official download page or use following command to download from shell

{% highlight text %}
# wget https://secure.logmein.com/labs/logmein-hamachi-2.1.0.139-1.x86_64.rpm
{% endhighlight %}

`logmein-hamachi` package depends on `redhat-lsb-core module`, install them using `yum`


{% highlight text %}
# yum install redhat-lsb-core logmein-hamachi-2.1.0.139-1.x86_64.rpm
{% endhighlight %}

If you type `ip addr` in shell, you can see a new interface called `ham0`

![ham0 Interface]({{ site.url }}/public/images/ham0.png)

### Joining to Hamachi Network

In LogmeIn page, in Networks section, go to My Networks and copy your network ID

In your host, use  `hamachi` command for request access to your network as following

{% highlight text %}
# hamachi login
Logging in .......... ok
# hamachi do-join 000-000-000
Joining 000-000-000 .. ok, request sent, waiting for approval
{% endhighlight %}

 **Note:** *`000-000-000` is your network ID*

Finally go to LogmeIn dashboard for approve host access, then hamachi assigns to `ham0` a valid IP in your Hamachi Network

![ham0 Ready]({{ site.url }}/public/images/ham1.png)