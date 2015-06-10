---
layout: post
title: Install Java 8 on Centos 7
---


### Downloading Latest Java Archive

Download latest Java SE Development Kit 8 release from its official download page or use following commands to download from shell

{% highlight bash %}
# cd /opt/
# wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u45-b14/jdk-8u45-linux-x64.tar.gz"
# tar xzfv jdk-8u45-linux-x64.tar.gz
{% endhighlight %}

 **Note:** *For production servers is highly recomended installing Java Server JRE. The Server JRE includes tools for JVM monitoring and tools commonly required for server applications, but does not include browser integration (the Java plug-in)*

{% highlight bash %}
# cd /opt/
# wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u45-b14/server-jre-8u45-linux-x64.tar.gz"
# tar xzfv server-jre-8u45-linux-x64.tar.gz
{% endhighlight %}

 **Note:** *If your Centos 7 is a fresh minimal installation, maybe you have to install wget utility using yum*

{% highlight bash %}
# yum -y install wget
{% endhighlight %}


### Configuring Environment Variables

Most of Java based application’s uses environment variables to work. CentOS provides of `/etc/profile.d/` directory for customizing environment variables per application

{% highlight bash %}
# echo "export JAVA_HOME=/opt/jdk1.8.0_45/" >> /etc/profile.d/java.sh
# echo "export JRE_HOME=/opt/jdk1.8.0_45/jre/" >> /etc/profile.d/java.sh
# echo "export PATH=\$PATH:\$JAVA_HOME/bin/:\$JRE_HOME/bin/" >> /etc/profile.d/java.sh
# source /etc/profile.d/java.sh
{% endhighlight %}

### Install Java with Alternatives

After setting Environment Variables use Alternatives to install Java

{% highlight bash %}
# alternatives --install /usr/bin/java java $JAVA_HOME/bin/java 1
# alternatives --config java

There are 3 programs which provide 'java'.

  Selection    Command
-----------------------------------------------
   1           /opt/jdk1.8.0_45/bin/java

Enter to keep the current selection[+], or type selection number: 1
{% endhighlight %}

At this point *Java 8* has been successfully installed on your system.

 **Note:** *If you performed this installation in a Development Server,  we also recommend to setup javac and jar commands path using alternatives*

{% highlight bash %}
# alternatives --install /usr/bin/jar jar $JAVA_HOME/bin/jar 1
# alternatives --install /usr/bin/javac javac $JAVA_HOME/bin/javac 1
# alternatives --set jar $JAVA_HOME/bin/jar
# alternatives --set javac $JAVA_HOME/bin/javac
{% endhighlight %}