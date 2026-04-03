FROM php:8.3-fpm-alpine

# 1. Cài các tool hệ thống cần thiết (cho máy i5 mượt mà)

RUN apk add --no-cache git unzip icu-dev

# 2. CHIÊU THỨC QUAN TRỌNG: Lấy file composer từ image chính thức

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# 3. Phân quyền sẵn cho user 1000

RUN chown -R 1000:1000 /var/www/html

USER 1000

```yml
laravel-php:
  build: . # Hoặc image: laravel-php-v1 nếu bạn đã build
  container_name: laravel-fpm
  user: "1000:1000"
  volumes:
    - ./laravel-app:/var/www/html
    - ~/git/ncc-packages:/home/cong/git/ncc-packages # Mount đúng đường dẫn tuyệt đối
  working_dir: /var/www/html
```

- ~/git/ncc-packages:/var/ncc-packages
