# Установка и настройка Apache
````
sudo apt install apache2
sudo systemctl enable apache2
sudo systemctl start apache2
netstat -tulpn | grep apache
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.64/26" port protocol="tcp" port="80" accept'
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.64/26" port protocol="tcp" port="443" accept'
firewall-cmd --reload
````

# Проверка тестовой страницы
````
curl http://localhost
````
# Создание каталогов для сайтов
````
sudo mkdir -p /var/www/test-site/{public_html,logs}
sudo mkdir -p /var/www/php-site/{public_html,logs}
sudo chown -R www-data:www-data /var/www
sudo chmod -R 755 /var/www
````

# Редактирование конфигурации Apache
````
sudo cp /etc/apache2/apache2.conf /etc/apache2/apache2.conf.backup
sudo nano /etc/apache2/apache2.conf
````
Include the virtual host configurations: IncludeOptional sites-enabled/*.conf

# Создание файла конфигурации виртуального хоста
````
sudo nano /etc/apache2/sites-available/test-site.conf
````

````
<VirtualHost *:80>
    ServerName test-site.local
    ServerAdmin webmaster@test-site.local
    DocumentRoot /var/www/test-site/public_html
    ErrorLog /var/www/test-site/logs/error.log
    CustomLog /var/www/test-site/logs/access.log combined
    <Directory /var/www/test-site/public_html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
````
# Активация сайта
````
sudo a2ensite test-site.conf
sudo systemctl reload apache2
````

# index.html
````
sudo nano /var/www/test-site/public_html/index.html
````

````
<!DOCTYPE html>
<html>
<head>
    <title>Test Site</title>
</head>
<body>
    <h1>Welcome to Test Site!</h1>
    <p>This is a test HTML page.</p>
</body>
</html>
````
````
curl http://localhost
````

# Установка PHP
````
sudo apt install php libapache2-mod-php php-mysql php-cli php-curl php-gd php-mbstring php-xml php-zip
sudo systemctl restart apache2
````
# Создание PHP виртуального хоста
````
sudo nano /etc/apache2/sites-available/php-site.conf
````
````
<VirtualHost *:80>
    ServerName php-site.local
    ServerAdmin webmaster@php-site.local
    DocumentRoot /var/www/php-site/public_html
    ErrorLog /var/www/php-site/logs/error.log
    CustomLog /var/www/php-site/logs/access.log combined
    <Directory /var/www/php-site/public_html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
````

# Активация сайта
````
sudo a2ensite php-site.conf
sudo systemctl reload apache2
````

# PHP файл
````
sudo nano /var/www/php-site/public_html/index.php
````
````
<?php phpinfo();
````
````
curl http://php-site.local
````