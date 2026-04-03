> docker run --rm composer:latest --version

Bước 1: Cấu hình lại "Cầu nối" (Docker Compose)

```yml
services:
  laravel-php:
    # ... các cấu hình cũ ...
    volumes:
      - ./laravel-app:/var/www/html
      # Thêm dòng này: Mount thư mục package vào đúng vị trí tương đối
      - ~/git/ncc-packages:/var/www/ncc-packages
```

Bước 2: Triệu hồi "Thợ phụ" Composer

```bash
docker run --rm \
  -v $(pwd):/app \
  -v ~/git/ncc-packages:/var/www/ncc-packages \
  composer install
```

Bước 3: Cấp lại "Chìa khóa" (Permissions)

sudo chown -R $USER:$USER vendor/

Bước 4: Làm sạch bộ nhớ đệm (Laravel Cache)

docker compose exec laravel-php php artisan optimize:clear

## fix 2

```bash
docker run --rm \
  -v $(pwd):/app \
  -v ~/git/ncc-packages:/ncc-packages \
  composer install
```

> ls -l vendor/ncc/

volumes:

- ./laravel-app:/var/www/html
- ~/git/ncc-packages:/ncc-packages # Phải khớp với lệnh docker run ở trên

Bạn đã thêm dòng mount vào, nhưng có thể bạn chưa recreate (khởi động lại hoàn toàn) container nên nó chưa nhận ổ đĩa mới.

docker compose up -d --force-recreate

docker compose exec laravel-php ls -l /ncc-packages

# fix 3

docker run --rm \
 -v $(pwd):/app/project \
 -v ~/git/ncc-packages:/ncc-packages \
 -w /app/project \
 composer install

# fix 4

services:

# 1. Khai báo ông thợ Composer

composer-bin:
image: composer:latest
volumes: - composer_data:/usr/bin

# 2. Container Laravel của bạn

laravel-php: # ... các cấu hình cũ ...
volumes: - ./laravel-app:/var/www/html - ~/git/ncc-packages:/ncc-packages # Mount chung thư mục bin để lấy lệnh composer - composer_data:/usr/local/bin
depends_on: - composer-bin

volumes:
composer_data:

composer-bin:
image: composer:latest
container_name: composer-helper # Buộc container này thoát nhanh sau khi copy file vào volume
command: ["true"]
volumes: - composer_data:/usr/bin/

# fix 4

# 1. Khai báo ông thợ Composer

composer-bin:
image: composer:latest
volumes: # Mount vào thư mục bin của composer để lấy file - composer_data:/usr/bin/

# --- NHÓM 1: LARAVEL (PHP 8.3) ---

laravel-php:
image: laravel-php-v1
container_name: laravel-fpm
user: "1000:1000"
volumes: - ./laravel-app:/var/www/html - ./shared-uploads:/var/www/html/public/uploads - ~/git/ncc-packages:/ncc-packages # Mount vào một thư mục TRUNG GIAN (không mount vào /usr/local/bin nữa) - composer_data:/opt/composer-bin:ro
depends_on:
db-shared:
condition: service_healthy

docker compose down -v

# 1. Khởi động hệ thống

docker compose up -d

# 2. Tạo link từ thư mục trung gian sang thư mục lệnh (Chạy quyền root)

docker compose exec -u root laravel-php ln -s /opt/composer-bin/composer /usr/local/bin/composer

# 3. Kiểm tra thành quả

docker compose exec laravel-php composer -v

# Mount vào /var/ để khi từ /var/www/html lùi 2 cấp sẽ thấy nó

      - ~/git/ncc-packages:/var/ncc-packages

docker exec -it --user www-data laravel-fpm php artisan tinker

docker exec -it mysql-shared mysql -uwp_admin -pwp_password wordpress_db

SELECT \* FROM wp_options WHERE option_name IN ('thumbnail_size_w', 'thumbnail_size_h', 'thumbnail_crop');

SELECT \* FROM wp_options
WHERE option_name IN (
'medium_size_w', 'medium_size_h',
'large_size_w', 'large_size_h'
);

UPDATE wp_options SET option_value = 0 WHERE option_name IN ('thumbnail_size_w', 'thumbnail_size_h', 'medium_size_w', 'medium_size_h', 'large_size_w', 'large_size_h');

UPDATE wp_options SET option_value = 0 WHERE option_name = 'medium_large_size_w';
