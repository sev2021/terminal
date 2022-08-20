## How to install phpmyadmin on CentOS 8
(CentOS latest release 8.2004 from June 2020).

CentOS Linux distribution derive from Red Hat Enterprise Linux source.  
Available for download from official CentOS Linux project website: https://www.centos.org/download/

At current time CentOS release 8.2004 has no profiled phpmyadmin package in any of official repositories and it require manual installation of all necessary libraries. Access to root account or user with sudo privileges required. For fresh centOS installation may need Network Connection Configuration with *nmtui* command:  
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
> sudo firewall-cmd --reload
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


**2. CentOS database installation**

Further to php you also need database – mysql or mariaDB to be installed and running. Here are commands to run mysql installation:

<pre>
> sudo dnf install mysql-server
</pre>

<pre>
> sudo systemctl status mysqld
> sudo systemctl enable mysqld
> sudo systemctl start mysqld
</pre>

To confirm that database works correctly you can run *mysqld -V* or open mysql console with command:

<pre>
> mysql -u root -p
</pre>

Flags „u” and „p” force mysql to start with specific user (root in this case) and with password request. If you run **mysql** command on its own or use sudo prefix, it will display „Access denied” alert. Default password for „root”  user is blank (empty), and it is good to change it first, using mysql console. Accessing mysql console *(mysql>)* allows any database operation like changing password, creating new user or listing user privileges :

<pre>
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password_here';
mysql> CREATE USER 'root'@'localhost' IDENTIFIED BY 'new_password_here';
mysql> SHOW GRANTS FOR 'root'@'localhost';
mysql> GRANT ALL PRIVILEGES ON database.* TO 'username'@'localhost';
mysql> QUIT;
</pre>

Working username with password will be required to log to phpmyadmin console. Finally it is good to ensure these two packages being installed:

<pre>
> sudo dnf install wget
> sudo snf install unzip
</pre>


**3. Phpmyadmin download and installation**

If there is still no dedicated CentOS 8 version of phpmyadmin in official repositories, best source of installation package is developer website:   https://www.phpmyadmin.net/downloads  
At the moment latest release is 5.0.2 but it is always good to check compatibility with other system elements and if requires download and install older release from developer archive:  
https://www.phpmyadmin.net/files/  
There is also choice for number of language and compression versions, we need zip file link to copy – https://files.phpmyadmin.net/phpMyAdmin/4.9.5/phpMyAdmin-4.9.5-all-languages.zip  

Before download go to main Apache folder */var/www/http/* – it is recommended to download and install phpmyadmin there. Some distributions use different locations like */usr/shares/* for example but CentOS works with main Apache folder.  

<pre>
cd /var/www/html/
sudo wget https://files.phpmyadmin.net/phpMyAdmin/4.9.5/phpMyAdmin-4.9.5-all-languages.zip
</pre>

After downloading extract installation files, change extracted folder name to *phpmyadmin8, add set required permissions to */tmp* folder :  

<pre>
> sudo unzip phpMyAdmin-4.9.5-all-languages.zip
> sudo mv phpMyAdmin-4.9.5-all-languages.zip  phpmyadmin
> sudo mkdir  phpmyadmin/tmp
> sudo chown -R apache:apache phpmyadmin
> sudo chmod 777  phpmyadmin/tmp
</pre>

In production environment it is important to set individual *blowfish_secret* variable in *config.inc.php*. Open file with nano command and set random 32 character string to line *$cfg[‚blowfish_secret’]= *.
Changes to Apache, phpmyadmin or related services setting may require Apache to restart, so do it with:  

<pre>
> sudo systemctl restart httpd.  
</pre>

One important security feature in some linux distributions (and all CentOS releases) is SELinux modue (https://en.wikipedia.org/wiki/Security-Enhanced_Linux). It provides additional security mechanisms and require additional setting for accessing system resources. Use *getenforce* to get SE level (Enforcine/Permissiove/Disabled). To allow Apache to use phpmyadmin file-context has to be set for *phpmyadmin* and *phpmyadmin/tmp* folders. To do it we have to install semanage utils and then run them:  

<pre>
> dnf install policycoreutils-python-utils
> semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/phpmyadmin/'
> semanage fcontext -a -t httpd_sys_rw_content_t "/var/www/html/phpmyadmin/tmp(/.*)?"
</pre>

To recurse through all the files in your phpmyadmin directory to activate *semanage* settings use *restorecon* command:  

<pre>
> restorecon -Rv '/var/www/html/phpmyadmin/'
</pre>

Now remember to restart apache server to update new settings:  

<pre>
> sudo systemctl restart httpd
</pre>

It is time to test phpmyadmin working. Get your server ip from *hostname -I* command and open with your browser *http://[server-ip]/phpmyadmin*:
