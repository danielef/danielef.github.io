---
layout: post
title: Install Glassfish 4 Cluster
---


### Network Scenario

We have three servers in two different physical locations

 Description | Network 1 
------------- |------------
Balancer | `ha.cognicio.us`
Admin | `das.cognicio.us`
Farm | `farm01.cognicio.us`
Farm | `farm02.cognicio.us`

Admin and Farm servers have private IPs and will access to each others via Private Network

### SELinux Configuration

Is important disable SELinux in Admin and Farm servers in `/etc/selinux/config` (You will need to reboot your systems).

{% highlight bash %}
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disable
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
{% endhighlight %}


Admin and Farm servers need to [Install of Java 8 on Centos 7](/install-java8-on-centos7/), also is mandatory create an user `glassfish` in each of this servers, passwords can differ (please!)

#### In Admin `das.cognicio.us`

{% highlight text %}
das# useradd glassfish
das# passwd glassfish
{% endhighlight %}


#### In Farm `farm01.cognicio.us`

{% highlight text %}
farm01# useradd glassfish
farm01# passwd glassfish
{% endhighlight %}

#### In Farm `farm02.cognicio.us`

{% highlight text %}
farm02# useradd glassfish
farm02# passwd glassfish
{% endhighlight %}


### Glassfish 4 Download and Directory Structure

First, we proceed to Glassfish 4 installation only in Admin

#### In Admin `das.cognicio.us`
{% highlight text %}
das# cd /opt
das# wget http://dlc.sun.com.edgesuite.net/glassfish/4.1/release/glassfish-4.1.zip
das# unzip glassfish-4.1.zip
das# chown -R glassfish:glassfish glassfish4
{% endhighlight %}

In following steps, the Glassfish 4.1 directory will be copied from Admin to Farm servers, therefore, we need to create an identical directory structure 

#### In Farm `farm01.cognicio.us`
{% highlight text %}
farm01# cd /opt
farm01# mkdir glassfish4
farm01# chown -R glassfish:glassfish glassfish4
{% endhighlight %}

#### In Farm `farm02.cognicio.us`
{% highlight text %}
farm02# cd /opt
farm02# mkdir glassfish4
farm02# chown -R glassfish:glassfish glassfish4
{% endhighlight %}

### Config Domain Creation

Is mandatory to create a domain for Glassfish in Admin as `glassfish` user. **Remember your admin Username and Password**

#### In Admin `das.cognicio.us`
{% highlight text %}
# cd /opt/glassfish4
# su glassfish
$ bin/asadmin create-domain dasdomain
Enter admin user name [Enter to accept default "admin" / no password]>admin
Enter the admin password [Enter to accept default of no password]> 
Enter the admin password again> 
Using default port 4848 for Admin.
Using default port 8080 for HTTP Instance.
Using default port 7676 for JMS.
Using default port 3700 for IIOP.
Using default port 8181 for HTTP_SSL.
Using default port 3820 for IIOP_SSL.
Using default port 3920 for IIOP_MUTUALAUTH.
Using default port 8686 for JMX_ADMIN.
Using default port 6666 for OSGI_SHELL.
Using default port 9009 for JAVA_DEBUGGER.
Distinguished Name of the self-signed X.509 Server Certificate is:
[CN=das.cognicio.us,OU=GlassFish,O=Oracle Corporation,L=Santa Clara,ST=California,C=US]
Distinguished Name of the self-signed X.509 Server Certificate is:
[CN=das.cognicio.us-instance,OU=GlassFish,O=Oracle Corporation,L=Santa Clara,ST=California,C=US]
Domain dasdomain created.
Domain dasdomain admin port is 4848.
Domain dasdomain admin user is "admin".
Command create-domain executed successfully.
{% endhighlight %}

**Note** *Do not forget to verify `/opt/glassfish4/glassfish/domains/dasdomain/config/domain.xml` file for any ocurrence of `localhost` (replace it by your hostname, in this case, `das.cognicio.us`)*

### DAS Domain First Start

Our `das.cognicio.us` server is ready for its first start using `asadmin` as follow:

#### In Admin `das.cognicio.us`
{% highlight text %}
das$ bin/asadmin start-domain dasdomain
Waiting for dasdomain to start ........
Successfully started the domain : dasdomain
domain  Location: /opt/glassfish4/glassfish/domains/dasdomain
Log File: /opt/glassfish4/glassfish/domains/dasdomain/logs/server.log
Admin Port: 4848
Command start-domain executed successfully.
{% endhighlight %}

To allow remote administration use `enable-secure-admin`
{% highlight text %}
das$ bin/asadmin enable-secure-admin
Enter admin user name>  admin
Enter admin password for user "admin"> 
You must restart all running servers for the change in secure admin to take effect.
Command enable-secure-admin executed successfully.
{% endhighlight %}

### Admin Firewall Rules

We need also configure a set of rules for firewall access.

Port | Usage
---- | -----
4848 |  Glassfish Administration Console
3820 |  IIOP
3920 |  IIOP
3700 |  IIOP

Using `firewall-cmd` add permanent access in each port as follows

#### In Admin `das.cognicio.us`
{% highlight text %}
das$ su - root
Password:
das# firewall-cmd --permanent --add-port=4848/tcp
das# firewall-cmd --permanent --add-port=3820/tcp
das# firewall-cmd --permanent --add-port=3920/tcp
das# firewall-cmd --permanent --add-port=3700/tcp
das# firewall-cmd --reload
{% endhighlight %}

### Installation of Glassfish 4.1 in Farm Servers

This task will be executed from `das.cognicio.us` server using `asadmin` utility.

To install to `farm01.cognicio.us`

#### In Admin `das.cognicio.us`
{% highlight text %}
das$ bin/asadmin install-node-ssh farm01.cognicio.us
Enter remote password for glassfish@farm01.cognicio.us>
{% endhighlight %}

And exactly to `farm02.cognicio.us`
#### In Admin `das.cognicio.us`
{% highlight text %}
das$ bin/asadmin install-node-ssh farm02.cognicio.us
Enter remote password for glassfish@farm01.cognicio.us>
{% endhighlight %}

### Nodes Creation

Go to Glassfish Administration Console in `https://das.cognicio.us:4848/`, login using Username and Password specified in **Config Domain Creation**

![login]({{ site.url }}/public/images/gf-cluster-01.png)

In left sidebar, go to Nodes section

![login]({{ site.url }}/public/images/gf-cluster-02.png)

Create a new Node with `New` button with following parameters and values

Parameter | Value
---------- | ------
Name | node01
Type | SSH
Node Host | farm01.cognicio.us
Node Directory | *empty*
Install Glassfish Server | unchecked
Force | unchecked
SSH Port | 22
SSH User Name | ${user.name}
SSH User Autentication | Password
SSH User Password  | *somesecret*

Click in `OK` button. 

![login]({{ site.url }}/public/images/gf-cluster-03.png)

Repeat this steps to create a second Node with following values

Parameter | Value
---------- | ------
Name | node02
Type | SSH
Node Host | farm02.cognicio.us
Node Directory | *empty*
Install Glassfish Server | unchecked
Force | unchecked
SSH Port | 22
SSH User Name | ${user.name}
SSH User Autentication | Password
SSH User Password  | *somesecret*

In Node section you can see 3 nodes: one of type `CONFIG` and two of type `SSH` (`node01` and `node02`). SSH nodes are running in two remote servers `farm01.cognicio.us` and `farm02.cognicio.us`.

![login]({{ site.url }}/public/images/gf-cluster-04.png)

### Cluster and Instances Creation

Go to Clusters section and add a new cluster with `New` button

![login]({{ site.url }}/public/images/gf-cluster-05.png)

Lets create this cluster with following values

Parameter | Value
---------- | ------
Cluster Name | cluster01
Configuration | default-config and Make a Copy of Selected Configuration
Message Queue Cluster Config Type | Default: Embedded Conventional Cluster with Master Broker

In the same screen, with `New` button, we create two instances using following values

Parameter |  Row1 | Row2
---------- | ------ | ------
Select | unchecked | unchecked
Instance Name | instance01 | instance02
Weight | *empty* | *empty*
Node | node01 | node02

![login]({{ site.url }}/public/images/gf-cluster-06.png)

Click in `OK` button and wait ...

![login]({{ site.url }}/public/images/gf-cluster-08.png)

If your screen looks like this, your cluster has been created successfully

![login]({{ site.url }}/public/images/gf-cluster-09.png)

Before to start the cluster, we must add some firewall rules in Farm servers `farm01.cognicio.us` and `farm02.cognicio.us`

### Farm Firewall Rules

In Glassfish Administration Console, go to `Configurations > cluster01-config > System Properties`

![login]({{ site.url }}/public/images/gf-cluster-11.png)

This table shows all ports used by `instance01` and `instance02`. Remember that this instances runs over Nodes `node01` and `node02` and each of this are installed in Farm servers `farm01.cognicio.us` and `farm02.cognicio.us`

Therefore, using `firewall-cmd` add permanent access in each port as follows

#### In Admin `farm01.cognicio.us`
{% highlight text %}
farm01# firewall-cmd --permanent --add-port=24848/tcp
farm01# firewall-cmd --permanent --add-port=28080/tcp
farm01# firewall-cmd --permanent --add-port=28181/tcp
farm01# firewall-cmd --permanent --add-port=23700/tcp
farm01# firewall-cmd --permanent --add-port=23820/tcp
farm01# firewall-cmd --permanent --add-port=23920/tcp
farm01# firewall-cmd --permanent --add-port=29009/tcp
farm01# firewall-cmd --permanent --add-port=27676/tcp
farm01# firewall-cmd --permanent --add-port=28686/tcp
farm01# firewall-cmd --permanent --add-port=26666/tcp
farm01# firewall-cmd --reload
{% endhighlight %}

#### In Admin `farm02.cognicio.us`
{% highlight text %}
farm02# firewall-cmd --permanent --add-port=24848/tcp
farm02# firewall-cmd --permanent --add-port=28080/tcp
farm02# firewall-cmd --permanent --add-port=28181/tcp
farm02# firewall-cmd --permanent --add-port=23700/tcp
farm02# firewall-cmd --permanent --add-port=23820/tcp
farm02# firewall-cmd --permanent --add-port=23920/tcp
farm02# firewall-cmd --permanent --add-port=29009/tcp
farm02# firewall-cmd --permanent --add-port=27676/tcp
farm02# firewall-cmd --permanent --add-port=28686/tcp
farm02# firewall-cmd --permanent --add-port=26666/tcp
farm02# firewall-cmd --reload
{% endhighlight %}

### Cluster Start

In Glassfish Administration Console, go to `Cluster`, select `cluster01` and click in `Start Cluster`

![login]({{ site.url }}/public/images/gf-cluster-12.png)

Finally, if you can see all instance icons in green, congratulations! the cluster is ready

![login]({{ site.url }}/public/images/gf-cluster-07.png)

### HA with Apache2

In Balancer `ha.cognicio.us` install Apache HTTPD and Core Utils

#### In Admin `ha.cognicio.us`
{% highlight text %}
ha# yum -y install httpd policycoreutils-python
{% endhighlight %}

Add security rules for 80 port using semanage and firewall-cmd

#### In Admin `ha.cognicio.us`
{% highlight text %}
ha# semanage port -a -t http_port_t -p tcp 80
ha# setsebool -P httpd_can_network_connect 1
ha# firewall-cmd --permanent --add-port=80/tcp
ha# firewall-cmd --reload
{% endhighlight %}

Start Apache HTTPD with systemctl

#### In Admin `ha.cognicio.us`
{% highlight text %}
ha# systemctl start httpd
{% endhighlight %}

{% highlight text %}
<VirtualHost *:80>
        ProxyRequests Off
	ProxyTimeout 10000
      	ProxyPreserveHost On
      	ProxyVia On
        
        ServerName cluster.cognicio.us

        <Proxy balancer://mycluster>
                # WebHead1
                BalancerMember http://192.168.1.101:28080/
                # WebHead2
                BalancerMember http://192.168.1.102:28080/

                # Security "technically we aren't blocking
                # anyone but this the place to make those
                # chages
                Order allow,deny
                Deny from none
                Allow from all

                # Load Balancer Settings
                # We will be configuring a simple Round
                # Robin style load balancer.  This means
                # that all webheads take an equal share of
                # of the load.
                # ProxySet lbmethod=byrequests
	        ProxySet lbmethod=bytraffic
                ProxySet stickysession=JSESSIONID

        </Proxy>

        # balancer-manager
        # This tool is built into the mod_proxy_balancer
        # module and will allow you to do some simple
        # modifications to the balanced group via a gui
        # web interface.
        <Location /balancer-manager>
                SetHandler balancer-manager

                # I recommend locking this one down to your
                # your office
                Order deny,allow
                Allow from all
        </Location>

        # Point of Balance
        # This setting will allow to explicitly name the
        # the location in the site that we want to be
        # balanced, in this example we will balance "/"
        # or everything in the site.
        
        #ProxyPass /clusterjsp balancer://mycluster/clusterjsp stickysession=JSESSIONID
	ProxyPass / balancer://mycluster/
        ProxyPassReverse /  balancer://mycluster/
        ProxyPassReverseCookieDomain balancer://mycluster/ http://cluster.cognicio.us	
</VirtualHost>
{% endhighlight %}