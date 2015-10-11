# configuration

Ключевые заметки для установки Open edX на сервер Ubuntu 14.04
Серым цветом выделены строчки для копирования в буфер обмена при установке платформы
0. Апгрейдим сервер до последней версии
sudo apt-get update -y
sudo apt-get upgrade -y
sudo reboot
1. Устанавливаем необходимые пакеты
sudo apt-get install -y build-essential software-properties-common python-software-properties curl git-core libxml2-dev libxslt1-dev libfreetype6-dev python-pip python-apt python-dev libxmlsec1-dev swig
sudo pip install --upgrade virtualenv
2. Создаём клон edX для установки
cd /var/tmp
git clone -b release https://github.com/edx/configuration
3. Вносим изменения nano /var/tmp/configuration/playbooks/roles/common/defaults/main.yml
COMMON_SSH_PASSWORD_AUTH to "yes"
4. Меняем (устанавливаем) пароль для mysql
проверяем, можем ли зайти mysql -u root -p
если нет то меняем... Подробности в статье http://webew.ru/articles/2104.webew
skip-grant-tables
flush privileges;
grant all ON *.* TO `root`@`localhost` identified by 'mypassword' with grant option;
Народный edX
2
5. Создаем файл /root/.my.cnf
----------- Начало .my.cnf ------------
[client]
user=root
password=<your root pass>
[mysql]
user=root
password=<your root pass>
[mysqldump]
user=root
password=<your root pass>
[mysqldiff]
user=root
password=<your root pass>
----------- Конец .my.cnf ------------
6. Поменять значения переменной EDXAPP_PYTHON_SANDBOX на False в playbooks/roles/edxapp/defaults/main.yml
7. Найти файл /var/tmp/configuration/playbooks/roles/edxapp/tasks/python_sandbox_env.
Поменять:
command: update-alternatives –set libblas.so.3gf /usr/lib/libblas/libblas.so.3gf
на:
command: update-alternatives –set libblas.so.3 /usr/lib/libblas/libblas.so.3
и в строке:
Народный edX
3
command: update-alternatives –set liblapack.so.3gf /usr/lib/lapack/liblapack.so.3gf
на:
command: update-alternatives –set liblapack.so.3 /usr/lib/lapack/liblapack.so.3
8. Исправить
/var/tmp/configuration/playbooks/roles/elasticsearch/tasks/main.yml
get_url: >
url=http://download.elasticsearch.org/elasticsearch/elasticsearch/{{ elasticsearch_file }}
9. Проверить /etc/update-motd.d
sudo apt-get install landscape-common
sudo apt-get install update-notifier-common
создаём 51-cloudguest
=========Начало 51-cloudguest =============
Файл может быть пустым
========Конец 51-cloudguest =============
Создаём
91-release-upgrade
=========Начало 91-release-upgrade ============
#!/bin/sh
# if the current release is under development there won't be a new one
if [ "$(lsb_release -sd | cut -d' ' -f4)" = "(development" ]; then
exit 0
fi
if [ -x /usr/lib/ubuntu-release-upgrader/release-upgrade-motd ]; then
exec /usr/lib/ubuntu-release-upgrader/release-upgrade-motd
fi
=========Конец 91-release-upgrade ============
Народный edX
4
10. Создать линк
sudo ln -s /usr/include/freetype2 /usr/include/freetype
11.
cd /var/tmp/configuration
sudo pip install -r requirements.txt
12. Запускаем инсталляцию
cd /var/tmp/configuration/playbooks && sudo ansible-playbook -c local ./edx_sandbox.yml -i "localhost,"
