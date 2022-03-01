# Chuẩn bị

> 1 Máy chủ Database (IP Address: 172.20.232.199)

> 1 Máy chủ Web (IP Address: 172.20.232.200)

# Cài đặt trên Máy Database

        sudo -i

        apt update

> Cài đặt dịch vụ MySQL

        apt install mysql-server


> Tạo database mẫu

        mysql -u root -p

        CREATE DATABASE my_data;
        
        CREATE USER 'my_user'@'172.20.232.200' IDENTIFIED BY '123456';

        GRANT ALL PRIVILEGES ON * . * TO 'my_user'@'172.20.232.200';

        FLUSH PRIVILEGES;

        EXIT

> Sửa file mysqld.cnf để có thể remote từ máy khác

        sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

> Sửa bind-address = 127.0.0.1 thành: 

![](https://i.imgur.com/ppCBSp7.png)

> Khởi động lại MYSQL

        service mysql restart

# Cài đặt trên Máy Web Server

> Tải mã nguồn Wordpress

        curl -O https://wordpress.org/latest.tar.gz

> Giải nén

        tar xzvf latest.tar.gz

> Copy thư mục Wordpress vào /var/www

        cp -r wordpress/ /var/www/

> Đi vào thư mục Wordpress

        cd /var/www/wordpress

> Copy file wp-config-sample.php ra file wp-config.php

        cp wp-config-sample.php wp-config.php

> Chỉnh sửa file wp-config.php

        nano wp-config.php

> 

        <?php
        define( 'DB_NAME', 'my_data' );

        /** Database username */
        define( 'DB_USER', 'my_user' );

        /** Database password */
        define( 'DB_PASSWORD', '123456' );

        /** Database hostname */
        define( 'DB_HOST', '172.20.232.199' );

        /** Database charset to use in creating database tables. */
        define( 'DB_CHARSET', 'utf8' );

        /** The database collate type. Don't change this if in doubt. */
        define( 'DB_COLLATE', '' );

        $table_prefix = 'wp_';

        define ('FS_METHOD', 'direct');

        define( 'WP_DEBUG', false );

        require_once ABSPATH . 'wp-settings.php';



![](https://i.imgur.com/P3d7LXL.png)

**10. Cấp quyền 777 cho thư mục Wordpress**

        chmod -R 777 ./*

**11. Cài các gói PHP cần thiết**

        add-apt-repository ppa:ondrej/php

        apt install php7.4-fpm php7.4-mysql

**12. Cấu hình Nginx**

        nano /etc/nginx/sites-available/default

> 

        server {
            listen 80 default_server;
            listen [::]:80 default_server;

            # SSL configuration
            #
            # listen 443 ssl default_server;
            # listen [::]:443 ssl default_server;

            root /var/www/wordpress;

            # Add index.php to the list if you are using PHP
            index index.html index.htm index.php;

            server_name 0.0.0.0;

            location / {
                    try_files $uri $uri/ =404;
            }
            location ~ \.php$ {
                    include snippets/fastcgi-php.conf;
                    fastcgi_pass unix:/run/php/php7.4-fpm.sock;
            }

        }

![](https://i.imgur.com/3u4fyVU.png)

**13. Khởi động lại Nginx**

        service nginx restart

**14. Gõ IP máy lên kiểm tra**

![](https://i.imgur.com/0qXNwMB.png)

