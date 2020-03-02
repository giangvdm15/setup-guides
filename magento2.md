# Magento 2 on Linux Ubuntu Installation Guide

## Prerequisites
- Know you to use Command Line/Terminal.
- Have [Composer](https://getcomposer.org/) installed (globally).

## 1. Setup Environment
### 1.1. Install and Configure Apache Server
#### Add Repository
```
sudo add-apt-repository ppa:ondrej/php
```
#### Install Apache2
```
sudo apt-get install apache2
```
#### Configure Apache2
Open up the configuration file:
```
nano /etc/apache2/sites-available/000-default.conf
```
Add these to the end of the file:
```
<Directory "/var/www/html">
	AllowOverride All
</Directory>
```
Save and close the file.<br/>
Open up the server hosting file:
```
nano /etc/apache2/apache2.conf
```
Add these to the end of the file:
```
ServerName localhost
```
Pay attention to the uppercase. Save and close the file.<br/>
Validate Apache2 configuration:
```
apachectl configtest
```
It should say ```Syntax OK```. If not, go back and check your configuration as done above.<br/>
Enable module rewrite:
```
sudo a2enmod rewrite
```
Restart Apache2 server:
```
sudo systemctl restart apache2
```
Allow Apache2 on Firewall:
```
sudo ufw allow 'Apache Full'
```

### 1.2. Install PHP and required extensions
#### Install PHP along with required extensions:
```
sudo apt-get install php7.2 libapache2-mod-php7.2 php-mysql php7.2-soap php7.2-bcmath php7.2-xml php7.2-mbstring php7.2-gd php7.2-common php7.2-cli php7.2-curl php7.2-intl php-zip -y
```
Since the current version of Magento 2 (2.3.3) is only compatible with PHP 7.2 and PHP 7.3, upgrading PHP to newer versions in the future will likely to cause issues. We need to prevent PHP from upgrading (it is called _hold_, see more [here](https://help.ubuntu.com/community/PinningHowto#Introduction_to_Holding_Packages)), by using commands like these:
```
sudo -s
echo php7.2 hold | dpkg --set-selections
```
replace ```php7.2``` with the package you want to hold, in this case, all the required PHP extensions above.
#### Configure .php file priority in Apache2 server:
```
nano /etc/apache2/mods-enabled/dir.conf
```
In the file, swap out the ```index.html``` with ```index.php```, so that the server will prioritize the .php files over the .html files. Save and close the file.
#### Restart Apache2 server:
```
sudo systemctl restart apache2
```

### 1.3. Install MySQL Database Management Server
#### Install MySQL:
```
sudo apt-get install mysql-server -y
```
#### Run MySQL Security script:
```
sudo mysql_secure_instalation
```
and follow as instructed.

### 1.4. Install PHPMyAdmin (highly recommended)
#### Install PHPMyAdmin:
```
sudo apt-get install phpmyadmin php-mbstring php-gettext -y
```
and follow as instructed.<br/>
**For Ubuntu 19.x and newer, currently, at this time of writing, PHPMyAdmin hasn't been available via default repository, so you have to install it manually with any method as shown below**
#### Method 1: Using Composer
[Watch this instructional video](https://www.youtube.com/watch?v=ZQPG_cOAi-g)
#### Method 2: Add Ubuntu 18.x repository, and then install it via command line.

#### After installing PHPMyAdmin, restart Apache2 server:
```
sudo systemctl restart apache2
```

### 1.5. Create new Magento user and setup folder permission, for security purpose (recommended)
#### Create a new user (optional)
```
sudo adduser magento
```
where ```magento``` is the name of the user, pick whatever you want. Then follow as instructed.<br/>
#### Allow new user to access Apache2 resources:
```
sudo usermod -g www-data magento
```
#### Set folder permission for Magento user:
```
sudo chown magento:www-data /var/www/html/
```
If you did not create a new user, then replace ```magento``` with your current username, for example, your username is ```john```, then run ```sudo chown john:www-data /var/www/html/```.

## 2. Prepare Magento 2
### 2.1. Obtain Magento Access Keys
Access [Magento Marketplace](https://marketplace.magento.com), login to your account, or create a new one if you haven't already. Then go to "My Profile" page, under "Marketplace" tab, look for "My Products" section, and hit "Access Keys". If you haven't had any access key, hit "Create A New Access Key" and follow as instructed. Finally, you should see something like
```
Public Key: xxx
Private Key: xxx
```
that's your access key which allows you to download any Magento (related) products.

### 2.2. Download Magento 2 via Composer
#### Go to Web server directory:
```
cd /var/www/html
```
#### Switch to Magento user (if you have created a new user as instructed in **1.5 section**):
```
sudo su magento
```
#### Create a project directory and enter it:
```
mkdir <project_name>
cd <project_name>
```
#### Download Magento 2 latest version:
```
composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition <installation_folder>
```
When the terminal shows ```Authentication required (repo.magento.com)``` and asks for Username and Password, go to your Magento Access Key page, copy and paste the Public Key and Private Key to the terminal (Public Key is your Username, and Private Key is your Password).<br/>
After that, Magento 2 will be downloading. Should you run into error on any packages that terminates the download, just run ```composer update <package_name>```, Composer will proceed to check the faulty package, and continue the download from where it left off.<br/>
When the download is complete, it should show up something like this
```
Writing lock file
Generating autoload files
```
#### Set permission for executable file before installing:
```
find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +
find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +
chown -R :www-data .
```

## 3. Install Magento 2
### 3.1. Create a Database
Go to [PHPMyAdmin](http://localhost/phpmyadmin), create a new database (for example, name it "magento2").
### 3.2. Run Magento 2 Installation
There are 2 methods to install Magento 2, via GUI and CLI. While installing via CLI tends to run you into complex errors, installing via GUI is recommended.
#### Method 1: Via GUI, run Setup Wizard
In your browser, type in your Magento 2 project URL (typically __http://localhost/<project_name>__), then add __/setup__ at the end (that makes it __http://localhost/<project_name>/setup__). The Setup Wizard will start, and just operate as instructed.<br>
At Step 1 (Readiness Check), you would probably encounter PHP Extensions Check error. If so, simply install the missing extensions (as listed) using the command ```sudo apt-get install <extension_name>```, and then re-run the check-up again.
#### Method 2: Via CLI, run setup command
```
php magento setup:install \
--base-url=http://localhost/<project_name>/ \
--db-host=localhost --db-name=<database_name> \
--db-user=<database_user, typicall 'root'> --db-password=<databse_password, typically ''> \
--admin-firstname=<first_name> --admin-lastname=<last_name> --admin-email=<email> \
--admin-user=<user_name> --admin-password=<password> --language=en_US \
--currency=USD --timezone=America/Chicago --cleanup-database \
--sales-order-increment-prefix="ORD$" --session-save=db --use-rewrites=1
```

### 3.3. Post-installation
#### Deploy Sample data (if desired)
```
php bin/magento sampledata:deploy
php bin/magento indexer:reindex
```
#### Deploy the application
```
php bin/magento setup:upgrade
php bin/magento setup:static-content:deploy -f
```
#### Update Memory limit
At the Magento project root directory, run:
```
nano .htaccess
```
In the .htaccess file, find ```php_value_memory_limit 756M```, and change ```756M``` to the desire value, ```2G``` is recommended. Save and close the file.
#### Install Cron tasks (optional)
```
php bin/magento cron:install
```
#### Deploy sample data (optional)
```
php bin/magento sample-data:deploy
```

## Installation complete!!!
