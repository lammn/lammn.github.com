---
layout: default
title: インストール方法
---

---

# Phương pháp cài đặt

## Chuẩn bị ban đầu

- Tiến hành cài đặt trước MySQL hoặc PostgreSQL Database
- Bỏ thư mục html vào DocumentRoot(htdocs...)
- Nếu không cho vào DocumentRoot thì đường dẫn sẽ là `http://{DocumentRoot}/{html path}`(No recommented)

## Phương pháp cài đặt

Để cài đặt EC-CUBE thì có 2 phương pháp như dưới đây.

- Shell script install
- Web install

## Cài đặt bằng shell script

Ở gần dòng 51 của file `eccube_install.sh` thì tùy vào môi trường mà sửa file này cho phù hợp rồi chạy nó.

- Nếu là PostgreSQL

```
sh eccube_install.sh pgsql
```

- Nếu là MySQL

```
sh eccube_install.sh mysql
```

Sau khi đã cài đặt xong thì truy cập vào đường dẫn `http://{install_url}/admin`
thì sẽ hiển thị màn hinh đăng nhập admin của EC-CUBE.
 Nhập username vs password như dưới đây.

`ID: admin PW: password`

## Cài đặt trên nền web

- Sử dụng composer để cài đặt thư viện bên ngoài

```
curl -sS https://getcomposer.org/installer | php
php ./composer.phar install --dev --no-interaction
```

- Truy cập vào đường dẫn install trên web

Truy cập vào link `http://{địa chỉ URL}/install/` sẽ hiển thị ra màn hình cài đặt EC-CUBE.
Làm theo hướng dẫn để cài đặt tiếp.




