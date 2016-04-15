---
layout: default
title: Plugin Development
---

---

#Plugin Development

Khi phát triển Plugin, muốn cài đặt chúng ta phải thao tác trên màn hình web.
Nhưng kể từ phiên bản EC-CUBE 3.0.9 trở đi, có thể dùng dòng lệnh để thực hiện các thao tác như nén
thư mục , cài đặt plugin, enable, disable plugin. Nó thực sự đã giúp ích rất nhiều cho các nhà phát triển

```
php app/console plugin:develop
```

#### Cách sử dụng của plugin:develop


Nếu sử dụng ```plugin:develop``` thì có thể thao tác với plugin bằng console


* example

```
php app/console plugin:develop install
php app/console plugin:develop uninstall
php app/console plugin:develop enable
php app/console plugin:develop disable
php app/console plugin:develop update
```


* Cách sử dụng console

Option ```--code[=CODE]``` ở đây `CODE` chính là plugin code trong file `config.yml`. Sẽ thực hiện các method tương ứng trong file
PluginManager.php

```
php app/console plugin:develop enable --code=plugincode
```
Trong ví dụ này sẽ thực thi method enable trong PluginManager.php


* Option dành riêng cho câu lệnh cài đặt plugin ```plugin:develop install```

```--path[=PATH]``` : path của plugin muốn install(đường dẫn này thực chất là đường dẫn đến file nén của
thư mục plugin mình muốn cài đặt)

```code``` : không cần chỉ ra option này trong câu lệnh install plugin.

```
php app/console plugin:develop install --path=/aaa/bbb/plugin.tar.gz
```

* Option dành riêng cho câu lệnh xóa plugin ```plugin:develop uninstall```

```--uninstall-force[=UNINSTALL-FORCE]``` : `[=UNINSTALL-FORCE]` nếu chỉ định là true sẽ remove plugin, mặc định là false

```
php app/console plugin:develop uninstall --code=plugincode --uninstall-force=true
```


