## Задание 1

### Установите Zabbix Server с веб-интерфейсом.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите PostgreSQL. Для установки достаточна та версия, что есть в системном репозитороии Debian 11.
3. Пользуясь конфигуратором команд с официального сайта, составьте набор команд для установки последней версии Zabbix с поддержкой PostgreSQL и Apache.
4. Выполните все необходимые команды для установки Zabbix Server и Zabbix Web Server.

#### Требования к результаты
* Прикрепите в файл README.md скриншот авторизации в админке.
* Приложите в файл README.md текст использованных команд в GitHub.

## Решение 1

Установка Postgresql

```
sudo su
apt update
apt install postgresql
```
Установка Zabbix

Добавляем репозиторий
```
wget https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/zabbix-release_6.0-5+debian12_all.deb
dpkg -i zabbix-release_6.0-5+debian12_all.deb
#ls -la /etc/apt/sources.list.d/
#cat /etc/apt/sources.list.d/zabbix.list
apt update
```
Установка Zabbix и веб-сервера
```
apt install zabbix-server-pgsql zabbix-frontend-php php8.2-pgsql zabbix-apache-conf zabbix-sql-scripts
systemctl status zabbix-server.service
```
Создание пользователя postgresql и базы данных
```
su - postgres -c 'psql --command "CREATE USER zabbix WITH PASSWORD '\'123456789\'';"'
su - postgres -c 'psql --command "CREATE DATABASE zabbix OWNER zabbix;"'
```
Импортирт начальной схемы базы данных
```
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
```
Настройка базы данных для Zabbix сервера
```
#find / -name zabbix_server.conf
#cat /etc/zabbix/zabbix_server.conf | grep DBP
sed -i 's/# DBPassword=/DBPassword=123456789/g' /etc/zabbix/zabbix_server.conf
```
или
```
nano /etc/zabbix/zabbix_server.conf
```
DBPassword=123456789

Перезапуск Zabbix сервера и веб-сервера и добавление в загрузку
```
systemctl restart zabbix-server apache2
systemctl enable zabbix-server apache2
```
Если при установке операционной системы выбрана RU-локаль, то установить US-локаль 
```
locale -a
locale-gen en_US.UTF-8
dpkg-reconfigure locales
```
![zabbix_web_login](img/zabbix-web-login.png)

---

## Задание 2

### Установите Zabbix Agent на два хоста.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите Zabbix Agent на 2 вирт.машины, одной из них может быть ваш Zabbix Server.
3. Добавьте Zabbix Server в список разрешенных серверов ваших Zabbix Agentов.
4. Добавьте Zabbix Agentов в раздел Configuration > Hosts вашего Zabbix Servera.
5. Проверьте, что в разделе Latest Data начали появляться данные с добавленных агентов.

#### Требования к результаты

1. Приложите в файл README.md скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу
2. Приложите в файл README.md скриншот лога zabbix agent, где видно, что он работает с сервером
3. Приложите в файл README.md скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
4. Приложите в файл README.md текст использованных команд в GitHub

## Решение 2
