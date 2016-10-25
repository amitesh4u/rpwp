# rpwp
Wordpress

## Install AMP on Ubuntu 16.04 

### Apache

    sudo apt-get update
    
    sudo apt-get install apache2

Check Configuration

    sudo apache2ctl configtest
    
    Output
    Syntax OK

If there is any warning 
    
    AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. 
    Set the 'ServerName' directive globally to suppress this message
 
 then we will add a single line to the /etc/apache2/apache2.conf

    ServerName server_domain_or_IP

Save, Close and test again.

Restart Apache to implement your changes

    sudo systemctl restart apache2

Spot test

    http://your_server_IP_address

### MySql

    sudo apt-get install mysql-server
    
  When the installation is complete, we want to run a simple security script that will remove some dangerous defaults and lock down access to our database system a little bit. Start the interactive script by running:
      
    sudo mysql_secure_installation



Warning: 
    
    Enabling this feature is something of a judgment call. 
    If enabled, passwords which don't match the specified criteria will be rejected by MySQL with an error. 
    This will cause issues if you use a weak password in conjunction with software which automatically 
    configures MySQL user credentials, such as the Ubuntu packages for phpMyAdmin. 
    It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.


Answer y for yes, or anything else to continue without enabling.

    VALIDATE PASSWORD PLUGIN can be used to test passwords
    and improve security. It checks the strength of password
    and allows the users to set only those passwords which are
    secure enough. Would you like to setup VALIDATE PASSWORD plugin?
    
    Press y|Y for Yes, any other key for No:
    
You'll be asked to select a level of password validation. Keep in mind that if you enter 2, for the strongest level, you will receive errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words.

    There are three levels of password validation policy:
    
    LOW    Length >= 8
    MEDIUM Length >= 8, numeric, mixed case, and special characters
    STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file
    
    Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
    
If you enabled password validation, you'll be shown a password strength for the existing root password, and asked you if you want to change that password. If you are happy with your current password, enter n for "no" at the prompt:

    Using existing password for root.
    
    Estimated strength of the password: 100
    Change the password for root ? ((Press y|Y for Yes, any other key for No) : n

For the rest of the questions, you should press Y and hit the Enter key at each prompt. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes we have made.



#### Create DB and insert data 

<OFFLINE>


### PHP


    sudo apt-get install php libapache2-mod-php php-mcrypt php-mysql
    
  In most cases, we'll want to modify the way that Apache serves files when a directory is requested. Currently, if a user requests a directory from the server, Apache will first look for a file called index.html. We want to tell our web server to prefer PHP files, so we'll make Apache look for an index.php file first.
  
  To do this, type this command to open the dir.conf file in a text editor with root privileges:
  
    sudo nano /etc/apache2/mods-enabled/dir.conf
  
  It will look like this:
  
      <IfModule mod_dir.c>
          DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
      </IfModule>
  
  We want to move the PHP index file highlighted above to the first position after the DirectoryIndex specification, like this:
  
      <IfModule mod_dir.c>
          DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
      </IfModule>
  
  When you are finished, save and close the file by pressing Ctrl-X. You'll have to confirm the save by typing Y and then hit Enter to confirm the file save location.
  
  After this, we need to restart the Apache web server in order for our changes to be recognized. You can do this by typing this:
  
     sudo systemctl restart apache2 
           or 
     sudo service apache2 restart
  
  We can also check on the status of the apache2 service using systemctl:
  
    sudo systemctl status apache2
  
  Sample Output
  ```
      ● apache2.service - LSB: Apache2 web server
         Loaded: loaded (/etc/init.d/apache2; bad; vendor preset: enabled)
        Drop-In: /lib/systemd/system/apache2.service.d
                 └─apache2-systemd.conf
         Active: active (running) since Wed 2016-04-13 14:28:43 EDT; 45s ago
           Docs: man:systemd-sysv-generator(8)
        Process: 13581 ExecStop=/etc/init.d/apache2 stop (code=exited, status=0/SUCCESS)
        Process: 13605 ExecStart=/etc/init.d/apache2 start (code=exited, status=0/SUCCESS)
          Tasks: 6 (limit: 512)
         CGroup: /system.slice/apache2.service
                 ├─13623 /usr/sbin/apache2 -k start
                 ├─13626 /usr/sbin/apache2 -k start
                 ├─13627 /usr/sbin/apache2 -k start
                 ├─13628 /usr/sbin/apache2 -k start
                 ├─13629 /usr/sbin/apache2 -k start
                 └─13630 /usr/sbin/apache2 -k start
      
      Apr 13 14:28:42 ubuntu-16-lamp systemd[1]: Stopped LSB: Apache2 web server.
      Apr 13 14:28:42 ubuntu-16-lamp systemd[1]: Starting LSB: Apache2 web server...
      Apr 13 14:28:42 ubuntu-16-lamp apache2[13605]:  * Starting Apache httpd web server apache2
      Apr 13 14:28:42 ubuntu-16-lamp apache2[13605]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerNam
      Apr 13 14:28:43 ubuntu-16-lamp apache2[13605]:  *
      Apr 13 14:28:43 ubuntu-16-lamp systemd[1]: Started LSB: Apache2 web server.
  ```


### Clone Project

clone project to the folder /var/www/html

    sudo git clone https://github.com/amitesh4u/rpwp.git .

Remember to put . at the end to ignore root folder name i.e. rpwp while cloning


### Secure folders

Edit apache2.conf or httpd.conf, as you find in Apache2 directory (probably located in /etc/apache2). You'll need to edit/check the following:

AllowOverride ALL (in the related <Directory> tag)

    <Directory /var/www/>
            Options Indexes FollowSymLinks
            AllowOverride All
            Require all granted
    </Directory>


Uncomment this line if present else Add

     AccessFileName .htaccess
     
Now add .htaccess file to any folder you want to secure with this line

    deny from all


ex: 

    cd .git
    sudo nano .htaccess
     [ add line "deny from all" ] 
     [ save and close ]

Restart apache2

    sudo service apache2 restart


