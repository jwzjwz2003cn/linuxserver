## linuxserver
In this project, the catalog application done in the previous part of the course is deployed to an AWS Lightsail instance.
The project is hosted on apache2 with mod_wsgi python version 2.7.12. Slight modification is made in the catalog application source code to accomodate the requirements of apache 2.

### SSH to the instance
```
$  ssh -i linuxCourse grader@18.216.218.156
```

### Catalog App URL
http://18.216.218.156.xip.io/

1. xip.io domain name service is used to accomodate google oauth requirement of not having ip only url as redirect url.

### Summary of installed software and config changes
#### Installed software

1. Git
2. Python 2.7.12
3. pip
4. mod_wsgi
5. postgresql
6. All python dependencies required to run catalog(flask, sqlalchemy etc)

#### Config changes

1. Firewall settings on UFW
```
ubuntu@ip-172-26-6-0:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----              
2200/tcp                   ALLOW       Anywhere                  
80/tcp                     ALLOW       Anywhere                  
123                        ALLOW       Anywhere                              
2200/tcp (v6)              ALLOW       Anywhere (v6)             
80/tcp (v6)                ALLOW       Anywhere (v6)             
123 (v6)                   ALLOW       Anywhere (v6)
```

2. Created new user grader
```
ubuntu@ip-172-26-6-0:~$ cat /etc/passwd
.
.
.
grader:x:1001:1001:Udacity Project Grader,,,:/home/grader:/bin/bash
```

3. Give grader sudo access, disable ssh with password
```
ubuntu@ip-172-26-6-0:~$ sudo vi /etc/sudoers.d/grader

grader ALL=(ALL) NOPASSWD:ALL
```

4. Generate ssh key pair on local machine and add .pub content to .ssh/authorized_keys on VM

5. Git clone catalog project to /var/www on VM

6. Edit apache2 config file at /etc/apache2/sites-available/000-default.conf with the following content
```
ubuntu@ip-172-26-6-0:/etc/apache2/sites-available$ cat 000-default.conf 
<VirtualHost *:80>
ServerAdmin webmaster@localhost
DocumentRoot /var/www/catalog

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined

WSGIScriptAlias / /var/www/catalog/myapp.wsgi
        <Directory /var/www/catalog>
           Order allow,deny
           Allow from all
        </Directory> 
</VirtualHost>
# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
ubuntu@ip-172-26-6-0:/etc/apache2/sites-available$ 
```

7. Create the myapp.wsgi file configured in 000-default.conf file in the app root directory
```
ubuntu@ip-172-26-6-0:/var/www/catalog$ cat myapp.wsgi 
import sys

sys.path.insert(0, '/var/www/catalog')

from project import app as application

application.secret_key = 'New secret key. Change it on server'

application.config['SQLALCHEMY_DATABASE_URI'] = (
    'sqlite:////var/www/catalog/catalog.db')
ubuntu@ip-172-26-6-0:/var/www/catalog$ 
```

8. Restart apache server
```
ubuntu@ip-172-26-6-0:sudo service apache2 restart
```

9. Update the OAuth 2.0 client IDs on Google API with vm ip address with xip.io at the end 

10. Update all packages
```
ubuntu@ip-172-26-6-0:sudo apt-get update
```
```
ubuntu@ip-172-26-6-0:sudo apt-get upgrade
```
11. changing port ssh port to 2200
```
grader@ip-172-26-6-0:~$ cat /etc/ssh/sshd_config | grep Port
Port 2200
```
12. PermitRootLogin to no
```
grader@ip-172-26-6-0:~$ cat /etc/ssh/sshd_config | grep PermitRootLogin
PermitRootLogin no
```

### Third party resources used
http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/
