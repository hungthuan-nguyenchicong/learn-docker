chmod -R 775 storage larave-app/bootstrap/cache

docker compose exec laravel-php chown -R 1000:1000 /var/www/html/storage /var/www/html/bootstrap/cache

# 1. Đảm bảo bạn đang ở đúng thư mục dự án

cd ~/git/learn-docker

# 2. Cấp quyền sở hữu cho chính bạn (user 1000) trên máy host

# Vì bạn dùng Bind Mount, quyền ở máy host sẽ phản chiếu vào container

sudo chown -R $USER:$USER laravel-app/storage laravel-app/bootstrap/cache

# 3. Cấp quyền ghi cho Group (775) để PHP-FPM có thể làm việc

chmod -R 775 laravel-app/storage laravel-app/bootstrap/cache

# Đứng từ thư mục learn-docker (nơi có file docker-compose.yml)

docker compose exec laravel-php php artisan config:clear

docker compose exec laravel-php php artisan cache:clear

docker compose exec laravel-php php artisan optimize:clear

docker compose exec --user root laravel-php docker-php-ext-install pdo_mysql

docker compose restart laravel-php

docker exec -it mysql-shared mysql -u root -p root_password -e "CREATE DATABASE IF NOT EXISTS laravel_db;"

docker exec -it mysql-shared mysql -u root -p

CREATE DATABASE IF NOT EXISTS laravel_db;

docker compose exec laravel-php php artisan migrate

docker compose exec laravel-php php artisan optimize:clear

docker exec -it mysql-shared mysql -uwp_admin -pwp_password wordpress_db

docker compose down -v
