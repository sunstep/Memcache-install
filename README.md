# Memcache-install

Step 1: Install APCu and Redis.
          sudo apt install php-apcu redis-server php-redis
          sudo service apache2 restart

Step 2: Edit the configuration file.
          sudo nano /etc/redis/redis.conf

- Ctrl+w to search “port” and change.
          port 6379
          to
          port 0
    
- Then Ctrl+w to search for and uncomment.
          unixsocket /var/run/redis/redis.sock
     
          unixsocketperm 700
          Change to
          unixsocketperm 770

- Ctrl+x, then ‘Y’ to save and exit.

- Let's check if the socket appeared in /var/run/redis/redis-server.sock

Step 3: Add the Redis user redis to the www-data group.
          sudo usermod -a -G redis www-data

Step 4: Restart Apache.
          sudo service apache2 restart

Step 5: Start Redis server.
          sudo service redis-server start

Step 6: Add the caching configuration to the Nextcloud config file.
          sudo nano /var/www/html/nextcloud/config/config.php

- Add the following right after the last line and before the ‘);’
    (before the last parenthesis and semicolon)

    'memcache.local' => '\OC\Memcache\APCu',
    'distributed' => '\\OC\\Memcache\\Redis',
    'memcache.locking' => '\\OC\\Memcache\\Redis',
    'filelocking.enabled' => 'true',
    'redis' => 
     array (
     'host' => '/var/run/redis/redis-server.sock',
     'port' => 0,
     'timeout' => 0.0,
    ),

- Ctrl + x, then ‘Y’ to save and exit.

- To verify Redis is enabled to start on boot:
       sudo systemctl enable redis-server

- Lets proceed fixing some errors in Security & Setup Warning. 

- Enable .htaccess
       sudo nano /etc/apache2/apache2.conf

       Then change
        <Directory /var/www/>        
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
        </Directory>

    To
       <Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
        </Directory>
    
- Save and quit, then restart Apache with:
        sudo service apache2 restart

- Change size of memory limit.
        sed -i '/^memory_limit =/s/=.*/= 512M/' /etc/php/7.4/apache2/php.ini

- Install recommended PHP modules.
       sudo apt install php7.4-bcmath
       sudo apt install php7.4-gmp
       sudo service apache2 restart

                  - End -
