---
layout: post
title: Install Glassfish 4 Cluster
---


### Network Scenario

We have three servers in two different physical locations

 Description | Network 1 | Network 2
------------- |------------ | -------------
 Firewall | `farm0.cognicio.us` | `farm1.cognicio.us`
Admin | `central.cognicio.us` |
Node | `node0.cognicio.us` |  `node2.cognicio.us`
Node | `node1.cognicio.us` | 

Admin and Node servers have private IPs and are accesibles via Firewall Servers using a simple port forwarding mechanism

Each Admin and Node servers are equal machines with a fresh [Install of Java 8 on Centos 7](/install-java8-on-centos7/), is mandatory create an user glassfish in each of this servers, passwords can differ (please!)

{% highlight text %}
node0# useradd glassfish
node0# passwd glassfish
{% endhighlight %}

### Download and Install Glassfish 4

First, we proceed to Glassfish 4 installation only in Admin server `central.cognicio.us`

{% highlight text %}
central# cd /opt
central# wget http://dlc.sun.com.edgesuite.net/glassfish/4.1/release/glassfish-4.1.zip
central# unzip glassfish-4.1.zip
central# chown -R glassfish:glassfish glassfish4
{% endhighlight %}

Is mandatory to create a domain for Glassfish

{% highlight text %}
central# cd /opt/glassfish4
central# su glassfish
central$ bin/asadmin
asadmin> create-domain --adminport 4848 centraldomain
Enter admin user name [Enter to accept default "admin" / no password]>admin
Enter the admin password [Enter to accept default of no password]> 
Enter the admin password again> 
Using port 4848 for Admin.
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
[CN=central,OU=GlassFish,O=Oracle Corporation,L=Santa Clara,ST=California,C=US]
Distinguished Name of the self-signed X.509 Server Certificate is:
[CN=central-instance,OU=GlassFish,O=Oracle Corporation,L=Santa Clara,ST=California,C=US]
Domain centraldomain created.
Domain centraldomain admin port is 4848.
Domain centraldomain admin user is "admin".
Command create-domain executed successfully.
asadmin> 
{% endhighlight %}

{% highlight text %}
asadmin> start-domain centraldomain
Waiting for centraldomain to start ........
Successfully started the domain : centraldomain
domain  Location: /opt/glassfish4/glassfish/domains/centraldomain
Log File: /opt/glassfish4/glassfish/domains/centraldomain/logs/server.log
Admin Port: 4848
Command start-domain executed successfully.
asadmin>
{% endhighlight %}

{% highlight text %}
asadmin> enable-secure-admin --port 4848
Enter admin user name>  admin
Enter admin password for user "admin"> 
You must restart all running servers for the change in secure admin to take effect.
Command enable-secure-admin executed successfully.
asadmin>
{% endhighlight %}

{% highlight text %}
central# semanage port -a -t http_port_t -p tcp 4848
central# firewall-cmd --permanent --add-port=4848/tcp
central# firewall-cmd --reload
{% endhighlight %}

{% highlight text %}
node1# mkdir /opt/glassfish4
node1# chown -R glassfish:glassfish /opt/glassfish4
{% endhighlight %}

{% highlight text %}
asadmin> install-node-ssh node1
Enter remote password for glassfish@node1>
Authenticating with password <concealed>
Authenticating with password <concealed>
Created installation zip /opt/glassfish4/glassfish9072453720586863332.zip
Authenticating with password <concealed>
Authenticating with password <concealed>
Copying /opt/glassfish4/glassfish9072453720586863332.zip (107575068 bytes) to node1:/opt/glassfish4
Installing glassfish9072453720586863332.zip into node1:/opt/glassfish4
Authenticating with password <concealed>
Removing node1:/opt/glassfish4/glassfish9072453720586863332.zip
Fixing file permissions of all bin files under node1:/opt/glassfish4
Fixing file permissions for nadmin file under node1:/opt/glassfish4/glassfish/lib
Command install-node-ssh executed successfully.
asadmin> 





 [glassfish@central glassfish4]$ bin/asadmin create-password-alias node1-alias
Enter the alias password> 
Enter the alias password again> 
Enter admin user name>  admin
Enter admin password for user "admin"> 
Command create-password-alias executed successfully.
[glassfish@central glassfish4]$ echo "AS_ADMIN_SSHPASSWORD=\${ALIAS=node1-alias}" >> node1.password
[glassfish@central glassfish4]$ bin/asadmin  install-node-ssh --passwordfile node1.password node1
Password is aliased. To obtain the real password, enter master password for exampledomain's key store>
Authenticating with password <concealed>
Authenticating with password <concealed>
Created installation zip /opt/glassfish4/glassfish7254357664837807752.zip
Authenticating with password <concealed>
Authenticating with password <concealed>
Copying /opt/glassfish4/glassfish7254357664837807752.zip (107575176 bytes) to node1:/opt/glassfish4
Installing glassfish7254357664837807752.zip into node1:/opt/glassfish4
Authenticating with password <concealed>
Removing node1:/opt/glassfish4/glassfish7254357664837807752.zip
Fixing file permissions of all bin files under node1:/opt/glassfish4
Fixing file permissions for nadmin file under node1:/opt/glassfish4/glassfish/lib
Command install-node-ssh executed successfully.
[glassfish@central glassfish4]$ bin/asadmin create-node-ssh --passwordfile node1.password --nodehost node1 node1
Enter admin user name>  admin
Enter admin password for user "admin"> 
Command create-node-ssh executed successfully.
{% endhighlight %}