## Home Work № 6  
---  
27.07.2021    
  
#### Встановити Wordpress  
#### Почати писати deployment скріпт на Bash    
Cкріпт має містити встановлення environment для CMS системи (wordpress, наприклад, або будь-якого іншого Frontend+Backend проекту на вибір) та встановлювати і налаштовувавти саму CMS.  
``` bash
#!/bin/bash

apt-get update

#________________Apache___________________
echo "Installing Apache..."
apt-get install apache2

SNAME=$(curl http://icanhazip.com)

echo "ServerName $SNAME" >> /etc/apache2/apache2.conf

if [$(apache2ctl configtest) = "Syntax OK" ];
then echo "Something wrong"
fi
systemctl restart apache2
ufw allow in "Apache Full"

#_________________MySQL____________________

if dpkg --get-selections | grep mysql
then
echo "MySQL aready exist"
else
echo "Installing MySQL..."
apt-get install mysql-server
apt-get install mysql-client
apt-get install php5-mysql
fi

DBNAME=n_db
DBUSER=n_user_db
DBPASS=passwd
ROOTPASS=Ad!08db11
DBDIR=/var/lib/mysql

echo "Creating user..."
mysql -u root -p "$ROOTPASS" -e "create user "$DBUSER"@'localhost' identified by '$DBPASS';"

echo "Creating database..."
if [-e "$DBDIR"/"$DBNAME"];
then
echo "Can\`t create. Already exist."
exit
fi

mysql -u root -p "$ROOTPASS" -e "create database "$DBNAME"; grant all on "$DBNAME".* to "$DBUSER"@'localhost'; flush privileges; "

if ["$?" != 0];
then
echo -e "\n Error with creating database."
exit
fi

echo -e "\n Database: "$DBNAME"
User: "$DBUSER"
PASSWORD: "$DBPASS" "

#_________________PHP________________

echo "Installing PHP..."
apt-get install php-curl php-gd php-mbstring php-mcrypt php-xml php-xmlrpc

#________________WordPress___________
DBPREFIX=prefix_
DBHOST=localhost
URL=http://stud.com
TITLE=stud
ADMINUSER=admin
ADMINPASS=Ad!08db11
ADMINEMAIL=yefimpfefferberh@gmail.com

wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
chmod +x wp-cli.phar
mv wp-cli.phar /usr/bin/wp

echo "Installing WordPress..."
wp core download --allow-root
wp core config --dbame="$DBNAME" --dbuser="$DBUSER" --dbpass="$DBPASS" --dbhost="$DBHOST" --dbprefix="$DBPREFIX" --allow-root
wp core install --erl="$URL" --title="$TITLE" --admin_user="$ADMINUSER" --admin_password="$ADMINPASS" --admin_email="$ADMINEMAIL" --allow-root

```  
