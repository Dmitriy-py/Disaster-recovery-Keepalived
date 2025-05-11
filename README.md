# Домашнее задание к занятию «Disaster recovery и Keepalived»

# Климов Дмитрий

## Задание 1
 1. Дана схема для Cisco Packet Tracer, рассматриваемая в лекции.
 2. На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
 3. Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
 4. Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.
 5. На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.

## Ответ:
![Снимок экрана (1033)](https://github.com/user-attachments/assets/873b7382-d7fb-4d04-bee5-03a8373b0a38)

![Снимок экрана (1034)](https://github.com/user-attachments/assets/de835eec-a472-4189-9a0c-17c8118a07d0)

![Снимок экрана (1035)](https://github.com/user-attachments/assets/e0fb4706-f417-44b7-ace7-f2da031bcc5a)

![Снимок экрана (1036)](https://github.com/user-attachments/assets/f0907531-0f71-4482-bdc9-f31e684e2d2b)

![Снимок экрана (1038)](https://github.com/user-attachments/assets/b5616cbc-0deb-4735-a9ba-c1ef58598f5c)

![Снимок экрана (1039)](https://github.com/user-attachments/assets/c96e66de-d67a-4214-a3e1-07685cfdb13e)

![Снимок экрана (1040)](https://github.com/user-attachments/assets/9fe4ed34-fec1-43fc-83f3-afe5f660ad92)

## Задание 2

 1. Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного файла.
 2. Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
 3. Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
 4. Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script
 5. На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

## Ответ:

keepalived.conf (master)

```
global_defs {
    router_id LVS_DEVEL
}

vrrp_script chk_webserver {
    script "/etc/keepalived/check_webserver.sh"
    interval 2
    weight -20
    fall 2
    rise 1
}

vrrp_instance VI_1 {
    state MASTER
    interface eth1
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.111.50
    }
    track_script {
        chk_webserver
    }
}

```

keepalived.conf (backup)

```
global_defs {
    router_id LVS_DEVEL
}

vrrp_script chk_webserver {
    script "/etc/keepalived/check_webserver.sh"
    interval 2
    weight -20
    fall 2
    rise 1
}

vrrp_instance VI_1 {
    state BACKUP
    interface eth1
    virtual_router_id 51
    priority 90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.111.50
    }
    track_script {
        chk_webserver
    }
}

```

check_webserver.sh


```
#!/bin/bash

WEB_PORT=80
WEB_ROOT="/var/www/html"
INDEX_FILE="index.html"

if ! nc -z localhost "$WEB_PORT"
then
  echo "Web server port $WEB_PORT is not accessible"
  exit 1
fi

if [ ! -f "$WEB_ROOT/$INDEX_FILE" ]
then
  echo "File $WEB_ROOT/$INDEX_FILE does not exist"
  exit 1
fi

exit 0

```

![Снимок экрана (1048)](https://github.com/user-attachments/assets/3ce4144a-a5c6-4b87-b4f9-402794ff1c71)

![Снимок экрана (1049)](https://github.com/user-attachments/assets/563f79cc-2b9f-4c7a-8a38-b945a237b267)

![Снимок экрана (1050)](https://github.com/user-attachments/assets/fffeda3f-fcb9-41fc-9285-445b8cf2fa19)

![Снимок экрана (1051)](https://github.com/user-attachments/assets/13d57022-f381-453e-ae6f-3e5c68749366)

![Снимок экрана (1052)](https://github.com/user-attachments/assets/156dcb21-7d8c-4ea8-ba71-5fc0f260eba0)










