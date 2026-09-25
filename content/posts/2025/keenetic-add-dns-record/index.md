---
title: "[RU] Добавление DNS-записей в роутерах Keenetic"
summary: "Добавляем DNS-записи в Keenetic без отдельного сервера"
date: 2025-07-31T18:01:09+02:00
tags:
  - linux
  - shell
draft: false
---

В роутерах Keenetic есть возможность добавлять DNS-записи без поднятия отдельного DNS-сервера.

1. Подключаемся к роутеру.
   ```console
   $ telnet <ip-адрес роутера>
   <вводим логин и пароль>
   ```

2. Добавляем DNS-запись для домена.
   - Меняем `<domain>` на нужный домен.
   - Меняем `<ip>` на IP-адрес, который вы хотите связать с доменом.

   ```console
   $ ip host <domain> <ip>
   $ system configuration save
   ```

3. Для удаления DNS-записи используем команду `no ip host`.
   ```console
   $ no ip host <domain>
   $ system configuration save
   ```
