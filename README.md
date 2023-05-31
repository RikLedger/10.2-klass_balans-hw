# Домашнее задание к занятию 2 «Кластеризация и балансировка нагрузки»  - `Горбачев Олег`

### Цель задания
В результате выполнения этого задания вы научитесь:
1. Настраивать балансировку с помощью HAProxy
2. Настраивать связку HAProxy + Nginx

### Чеклист готовности к домашнему заданию
1. Установлена операционная система Ubuntu на виртуальную машину и имеется доступ к терминалу
2. Просмотрены конфигурационные файлы, рассматриваемые на лекции.
---

### Задание 1
- Запустите два simple python сервера на своей виртуальной машине на разных портах.
- Установите и настройте HAProxy, воспользуйтесь материалами к лекции.
- Настройте балансировку Round-robin на 4 уровне.
- На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy.

### Решение 1
*создаём директории*
```shell
mkdir http1
```
```shell
mkdir http2
```
*в директориях http1 и http2 создаём файл index.html*
```shell
nano index.html
```
*и прописываем "Server 1 :8888" и "Server 2 :9999"*

*запускаем два простых python server*
```shell
python3 -m http.server 8888 --bind 0.0.0.0
```
```shell
python3 -m http.server 9999 --bind 0.0.0.0
```
*проверям их*
```shell
curl http://localhost:8888
```
```shell
curl http://localhost:9999
```
*установим вебсервер nginx*
```shell
sudo apt-get install nginx
```
*последовательно выпонлняем*
```shell
nano /etc/nginx/nginx.conf
nano /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl start nginx
```
*создаем файл*
```shell
nano /etc/nginx/conf.d/example-http.conf
```
```shell
include /etc/nginx/include/upstream.inc;

server {
   listen	80;
   

   server_name	example-http.com;
   

   access_log	/var/log/nginx/example-http.com-acess.log;
   error_log	/var/log/nginx/example-http.com-error.log;

   location / {
		proxy_pass	http://example_app;

   }

}
```
*создаем файл*
```shell
sudo mkdir /etc/nginx/include
sudo nano /etc/nginx/include/upstream.inc
```
```shell
upstream example_app {

	server 127.0.0.1:8888;
  server 127.0.0.1:9999;

}
```
*перезапускаем сервер*
```shell
sudo systemctl reload nginx
```
```shell
curl -H 'Host: example-http.com' http://localhost
```
![1-1](./8.2-1-001.jpg)
------
### Задание 2
- Запустите три simple python сервера на своей виртуальной машине на разных портах
- Настройте балансировку Weighted Round Robin на 7 уровне, чтобы первый сервер имел вес 2, второй - 3, а третий - 4
- HAproxy должен балансировать только тот http-трафик, который адресован домену example.local
- На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy c использованием домена example.local и без него.

### Решение 2

---
