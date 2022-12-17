## INSTALLING THE NGINX WEB SERVER

## *Displaying web pages to site visitors*

`In order to allow visitors to see my web pages, I need to install nginx.`
`However, before installing nginx, I updated my server by running command "sudo apt update" and the output displayed in the image below was generated`

![update](./Images2/sudo-apt-update.jpg)

`After the server has been updated, I run command "sudo apt install nginx" and the output generated is presented in the image below:`

![installing nginx](./Images2/sudo-apt-install-nginx.jpg)

`To confirm ngnix installation, I run command "Sudo systemctl status nginx" and the output in the image below was generated`

![confirming installation](./Images2/sudo-systemctl-status-nginx.jpg)

## *opening TCP port 80*

`In order to receive traffic by the web server, I opened TCP port 80 on my ubuntu server by running command "curl http://localhost:80" and I generated the output in the image shown below:`

![accessing TCP port 80](./Images2/accessing-TCP-port-80.jpg)

`I checked if my installation is working well by browsing http://(my ip address):80 on my browser and I got the image below from my browser.`

![testing my installation](./Images2/testing-installation-on-browser.jpg)


## INSTALLING MYSQL

`To install mysql, I run command "sudo apt install mysql-server" and I got the output displayed in the images below`

![installing mysql](./Images2/sudo-apt-install-mysql-1.jpg)

![installing mysql](./Images2/sudo-apt-install-mysql-2.jpg)

`After the installation was done. logged into the mysql console by running the command "sudo mysql" and I generated the output shown in the image below:`

![log in to mysql console](./Images2/sudo-mysql.jpg)

`After logging into mysql console, I defined the user password while the root password was set as mysql_native_password and the exit the mysql by running command "exit"`

`I started an interactive script by running the command "sudo mysql_secure_installation" in order to set security and generated the images below as output.`

![securing mysql](./Images2/security-mysql-1.jpg)

![securing mysql](./Images2/security-mysql-2.jpg)

`To confirm if mysql security is workin as expected, I run command "sudo mysql -p" and output in the image displayed below was generated`

![confirming password](./Images2/confirming-mysql-security.jpg)


## INSTALLING PHP

## *creating bridge between php interpreter and web server*

`To create bridge between php interpreter and the web server, I run command "sudo apt install php-fpm php-mysql" and the below image was generated as output.`

![php components](./Images2/installing-php-components.jpg)


## CONFIGURING NGINX TO USE PHP PROCESSOR

`lempstackproject was used as domain name. Therefore, a directory was created for it by running the command "sudo mkdir /var/www/lempstackproject"`

`To create owner for the directory, I run command "sudo chown -R $USER:$USER /var/www/lempstackproject"`

`Then, I created a configuration file in Nginx's sites-available directory using nano command as command-line editor and I run command "sudo nano /etc/nginx/sites-available/lempstackproject"`

`I then create a new blank file by running the command below:`

```
{#/etc/nginx/sites-available/lempstackproject

server {
    listen 80;
    server_name lempstackproject www.lempstackproject;
    root /var/www/lempstackproject;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}

}
```

`I then tested my configuration for syntax error by running command "sudo nginx -t"`

`The image below shows the commands I run and the output I got when I tested my configuration.`

`I disabled the default Nginx host by running command "sudo unlink /etc/nginx/sites-available/default" and I reloaded the Nginx with command "sudo systemctl reload nginx"`

`To confirm that my configuration is workin as expected, I created a new index.html file in the var/www/lempstackproject as it was initially empty`

`So, I added the command "sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/lempstackproject/index.html"`


![php conrfiguration confirmation](./Images2/php-configuration-confirmation.jpg)

## TESTING PHP WITH NGINX

To validate if Nginx can correctly hand .php to the php processor that I configured. To do this, I opened a new file named info.php by running the command below:

`sudo nano /var/www/lempstackproject/info.php` and a blank space output was generated in which run command 

```
<?
phpinfo();

```
The image in the output below was generated and this validated the correctness of my configuration.

![validating configuration](./Images2/testing-php-with-nginx.jpg)

Because the output generated above contains sensitive information about the PHP environment and my Ubuntu server, I used the command `sudo rm /var/www/lempstackproject/info.php`


## RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

A test database with simple "To do list" was created and access into it was configured in order to enable Nginx website to query data from the database and also display it.

To do this, I logged in to Mysql console using the root account by running command `sudo mysql`

To create a new database, I run command `CREATE DATABASE 'example_database';` and the output below was generated.

![create database](./Images2/create-database.jpg)

I created a new user named olaniyi_user and mysql_native_password was used as default authentication method. So, I defined olaniyi_user password as OLAola27@_?

After that, I give permission to the new user over the olaniyi_database database by running the command `GRANT ALL ON olaniyi_database.* TO 'olaniyi_user'@'%';` and the output below was generated

![creating new user](./Images2/creating%20new%20user.jpg)

To test if the access given to the new user is working, I exit the mysql console and then login to the olaniyi_user console by running  command `-u olaniyi_user -p` and the image below displays the output generated.

![Testing new user password](./Images2/testing-new-user-password.jpg)

To confirm that new user has access to the database, command `SHOW DATABASES;` was run and the output generated was shown in the image below:

![confirming new user database access](./Images2/confirming-new-user-database-access.jpg)

After testing olaniyi_user access to the database, I created a test tabled called todo_list by running command:


`CREATE TABLE olaniyi_database.todo_list (`
`item_id INT AUTO_INCREMENT,`
`content VARCHAR(255),`
`PRIMARY KEY(item_id)`
`);`

I then added "My first important item in to the test table" by running command `INSERT INTO olaniyi_database.todo_list (content) VALUES ("My first important item");`

I added other items by following the same command but with different values.

To confirm if the items I created has been added as expected, I run command `SELECT * FROM olaniyi_database.todo_list;` and the output below was generated.


![todo list](./Images2/items-added-to-todolist.jpg)

After this has been confirmed, I exit the mysql console. So, I  create a PHP script that needs to connect Mysql and query for my content. I therefore, created a new PHP file in my custom web root directory with vi editor by running the command `sudo nano /var/www/lempstackproject/todo_list.php`

To connect the query for the content of the todo_list table that I created to the mysql database the script below was run in order to display the results in a list.



`<?php`
`$user = "olaniyi_user";`
`$password = "OLAola27@_?"`
`$database = "olaniyi_database";`
`$table = "todo_list";`

`try {`
  `$db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);`
  `echo "<h2>TODO</h2><ol>";`
  `foreach($db->query("SELECT content FROM $table") as $row) {`
    `echo "<li>" . $row['content'] . "</li>";`
   `}`
   `echo "</ol>";`
`} catch (PDOException $e) {`
    `print "Error!: " . $e->getMessage() . "<br/>";`
    `die();`

`}`

I then accessed the page through my browser by http://my_IP/todo_list.php and the output below was generated on the browser

![Testing my php full configuration](./Images2/testing-php-configuration.jpg)
