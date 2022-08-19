## How to install phpmyadmin on CentOS 8
(CentOS latest release 8.2004 from June 2020).

CentOS Linux distribution derive from Red Hat Enterprise Linux source.  
Available for download from official CentOS Linux project website: https://www.centos.org/download/

At current time CentOS release 8.2004 has no profiled phpmyadmin package in any of official repositories and it require manual installation of all necessary libraries. Access to root account or user with sudo privileges required. For fresh centOS installation may need Network connection Configuration with *nmtui* command:  
<pre>
> nmtui
</pre>
https://labunhome.files.wordpress.com/2019/02/edit-network-interface-connection.png  
Confirm successful network configuration with system command:  
<pre>
> hostname -I
</pre>
It should return current system IP(s).

CentOS basic package manages YUM (2005) got later updated with new faster and safer DNF (2013) manager, but they both will work with commands in this tutorial. Good practice for packets installation, especially directly after fresh installation is running overall packages update first (it can take few minutes depending of system configuration):  
<pre>
> sudo dnf update
</pre>


**1. CentOS web server installation**

Some of packages listed below may be already installed and I tried to limit number of added packages to minimum. First required package is network server – nginx or apache for example. Httpd package (http–deamon) is Apache2 server for CentOS:

<pre>
> sudo dnf install httpd
</pre>

Confirm succesfull httpd installaton, set it running and for automatic start with command systemctl:

<pre>
> sudo systemctl status httpd
> sudo systemctl enable httpd
> sudo systemctl start httpd
</pre>

To confirm Apache server working you can run command *httpd -V*. Although it still should not be available in browser on *http://localhost*  as preinstalled CentOS firewall by default blocks http connections. To check firewall status you can run command *service firewall status* or *systemctl status firewalld*, but to see and change firewall setting you need *firewall-cmd* command:

<pre>
> sudo firewall-cmd --list-all  
> sudo firewall-cmd --permanent --add-service=http  
> sudo firewall-cmd --reload`  
</pre>
  
Now it is time to connect to *http://localhost* in web browser to confirm that Apache server works:  
https://labunhome.files.wordpress.com/2019/02/centos_apache.png  
Apache installation should automatically include all necessary php packages. You can create index.php file including phpinfo() function to see detailed information about php configuration. I still recommend to go trough list of packages below and run all of them to ensure none is missing:

<pre>
> sudo dnf install php  
> sudo dnf install php-pdo  
> sudo dnf install php-json  
> sudo dnf install php-cli  
> sudo dnf install php-mbstring  
> sudo dnf install php-mysqlnd
</pre>

