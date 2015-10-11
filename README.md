# **Ключевые заметки для установки Open edX на сервер Ubuntu 14.04**

**Шаг 0.** Апгрейдим сервер до последней версии

* `sudo apt-get update -y`
* `sudo apt-get upgrade -y`
* `sudo reboot`

**Шаг 1.** Устанавливаем необходимые пакеты

Для удобства управления и отладки устанавливаем файловый менеджер MC редактор NANO и почтовый клиент MUTT.
_Если вы не считаете нужным установку этих пакетов, то данный шаг можно пропустить._

`sudo apt-get install mc nano mutt`

**Шаг 2.** Проверяем установлен ли сервис MySQL на вашем хостинге

`mysql -u root -p`

_Если вы сумели зайти в СУБД MySQL то шаг можно пропустить._

_Если СУБД не установлена то_ ...

`sudo apt-get install mysql-client mysql-server`

В процессе установки будет предложено установить пароль суперпользователя. Запомните его он еще пригодится!
Если пароль утерян, то меняем (устанавливаем) его...
Подробности в статье http://webew.ru/articles/2104.webew

Ключевые фразы из этой статьи следующие:

1. skip-grant-tables
2. flush privileges;
3. grant all ON *.* TO `root`@`localhost` identified by 'mypassword' with grant option;

**Шаг 3.** Устанавливаем пакеты для сборки платформы Open edX

* `sudo apt-get install -y build-essential software-properties-common python-software-properties curl git-core libxml2-dev libxslt1-dev libfreetype6-dev python-pip python-apt python-dev libxmlsec1-dev swig`
* `sudo pip install --upgrade virtualenv`

**Шаг 4.** Создаём клон edX для установки
* `cd /var/tmp`
* `git clone -b release https://github.com/edx/configuration`

**Шаг 5.** Вносим изменения в файл main.yml расположенный по адресу
 
_nano /var/tmp/configuration/playbooks/roles/common/defaults/main.yml_

COMMON_SSH_PASSWORD_AUTH to "yes"

**Шаг 6.** Создаем файл /root/.my.cnf

_----------- Начало .my.cnf ------------_

`[client]`

`user=root`

`password=<your root pass>`

`[mysql]`

`user=root`

`password=<your root pass>`

`[mysqldump]`

`user=root`

`password=<your root pass>`

`[mysqldiff]`

`user=root`

`password=<your root pass>`

_----------- Конец .my.cnf ------------_

**Шаг 7.** Если ваш VPS хостинг поддерживает виртуализацию OpenVZ то следует поменять значения переменной 

EDXAPP_PYTHON_SANDBOX 

на False в файле main.yml 

_/var/tmp/configuration/playbooks/roles/edxapp/defaults/main.yml_

_Если тип виртуализации KVM, то шаг можно пропустить_

**Шаг 8.** В версии Ubuntu 14.04 изменились названия библиотек по сравнению с базовой версией Ubuntu 12.04 от разработчиков Open edX. Следует найти файл 

_/var/tmp/configuration/playbooks/roles/edxapp/tasks/python_sandbox_env._

и поменять строчки:

``command: update-alternatives –set libblas.so.3gf /usr/lib/libblas/libblas.so.3gf``

на

`command: update-alternatives –set libblas.so.3 /usr/lib/libblas/libblas.so.3`

и 

`command: update-alternatives –set liblapack.so.3gf /usr/lib/lapack/liblapack.so.3gf`

на

`command: update-alternatives –set liblapack.so.3 /usr/lib/lapack/liblapack.so.3`

**Шаг 9.** Найдите ключ get_url: > в файле main.yml 

`/var/tmp/configuration/playbooks/roles/elasticsearch/tasks/main.yml`

и исправьте на 

_url=http://download.elasticsearch.org/elasticsearch/elasticsearch/{{ elasticsearch_file }}_

**Шаг 10.** Проверьте в каталоге `/etc/update-motd.d` наличие файлов 51-cloudguest и 91-release-upgrade. Для достоверности выполните команды

* `sudo apt-get install landscape-common`
* `sudo apt-get install update-notifier-common`

создаём 51-cloudguest

=========Начало 51-cloudguest =============

Файл может быть пустым

========Конец 51-cloudguest =============

Создаём

91-release-upgrade

=========Начало 91-release-upgrade ============

`#!/bin/sh`

`# if the current release is under development there won't be a new one`

`if [ "$(lsb_release -sd | cut -d' ' -f4)" = "(development" ]; then`

`exit 0`

`fi`

`if [ -x /usr/lib/ubuntu-release-upgrader/release-upgrade-motd ]; then`

`exec /usr/lib/ubuntu-release-upgrader/release-upgrade-motd`

`fi`

=========Конец 91-release-upgrade ============

**Шаг 11.** В версии Ubuntu 14.04 изменился путь для библиотек. Создаем на них линк

`sudo ln -s /usr/include/freetype2 /usr/include/freetype`

**Шаг 12.** Запускаем конфигурирование установки Open edX

* `cd /var/tmp/configuration`
* `sudo pip install -r requirements.txt`

**Шаг 13.** Запускаем инсталляцию Open edX

`cd /var/tmp/configuration/playbooks && sudo ansible-playbook -c local ./edx_sandbox.yml -i "localhost,"`

При отсутствии ошибок инсталляции платформа будет установлена... 
