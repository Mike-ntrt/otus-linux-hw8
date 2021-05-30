# Linux HW 8 Systemd

Vagantfile содержит описание ВМ:  

`systemd 192.168.11.101`

Эти ВМ так же описаны в inventory-файле `inventories/all.yml`  

Плэйбук `systemd.yml` вызывает 3 роли из диретокрии `roles`

1. service_grep: грепает `/var/log/messages` и записывает вывод в `/var/grep_log.log`

2. spawn_fcgi: ставим пакеты 
    - spawn-fcgi
    - httpd
    - php
    - php-cli
    - mod_fcgid  

   Копируем юнит spawn-fcgi.service.j2 и конфиг spawn-fcgi.j2
   После релоада конфигов статус сервиса:

   ```
   [root@systemd vagrant]# systemctl status spawn-fcgi.service 
   ● spawn-fcgi.service - Spawn-fsgi service
      Loaded: loaded (/etc/systemd/system/spawn-fcgi.service; enabled; vendor preset: disabled)
      Active: active (running) since Sat 2021-05-29 23:44:34 UTC; 37min ago
    Main PID: 5988 (php-cgi)
      CGroup: /system.slice/spawn-fcgi.service
              ├─5988 /usr/bin/php-cgi
              ├─5990 /usr/bin/php-cgi
              ├─5991 /usr/bin/php-cgi
              ├─5992 /usr/bin/php-cgi
              ├─5993 /usr/bin/php-cgi
              ├─5994 /usr/bin/php-cgi
              ├─5995 /usr/bin/php-cgi
              ├─5996 /usr/bin/php-cgi
              ├─5997 /usr/bin/php-cgi
              ├─5998 /usr/bin/php-cgi
              ├─5999 /usr/bin/php-cgi
   ```

3. httpd_template: копируем юнит-файл httpd в `/etc/systemd/system/httpd@.service` и меняем:
   `EnvironmentFile=/etc/sysconfig/httpd-%i`
   После добавляем конфиг-файл `/etc/sysconfig/httpd-1` и запускаем сервис httpd@1.service
   ```
   [root@systemd vagrant]#systemctl status httpd@1
   ● httpd@1.service
   Loaded: loaded (/etc/systemd/system/httpd@.service; bad; vendor preset: disabled)
   Active: active (running) since Sun 2021-05-30 00:12:56 UTC; 16min ago
   Main PID: 8442 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/system-httpd.slice/httpd@1.service
           ├─8442 /usr/sbin/httpd -DFOREGROUND
           ├─8443 /usr/sbin/httpd -DFOREGROUND
           ├─8444 /usr/sbin/httpd -DFOREGROUND
           ├─8445 /usr/sbin/httpd -DFOREGROUND
           ├─8446 /usr/sbin/httpd -DFOREGROUND
           ├─8447 /usr/sbin/httpd -DFOREGROUND
           └─8448 /usr/sbin/httpd -DFOREGROUND

   May 30 00:12:56 systemd systemd[1]: Starting httpd@1.service...
   ``` 
Проект использует pipenv для установки ansible  
Как установить проект:  

```
cd otus-linux-hw8
pip install pipenv
pipenv shell
pipenv install
```

Как запустить проект:
```
vagrant up
ansible-playbook systemd.yml -v
```
