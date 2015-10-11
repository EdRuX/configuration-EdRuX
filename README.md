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

_Если СУБД не установлена то_

`sudo apt-get install mysql-client mysql-server`

В процессе установки будет предложено установить пароль суперпользователя. Запомните его он еще пригодится!
Если пароль утерян, то меняем (устанавливаем) его...
Подробности в статье http://webew.ru/articles/2104.webew

Ключевые фразы из этой статьи следующие:
1. skip-grant-tables
1. flush privileges;
1. grant all ON *.* TO `root`@`localhost` identified by 'mypassword' with grant option;

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
