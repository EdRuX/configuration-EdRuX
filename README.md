# configuration
# **Ключевые заметки для установки Open edX на сервер Ubuntu 14.04**

**Шаг 0.** Апгрейдим сервер до последней версии

* `sudo apt-get update -y`
* `sudo apt-get upgrade -y`
* `sudo reboot`

**Шаг 1.** Устанавливаем необходимые пакеты

Для удобства управления и отладки устанавливаем файловый менеджер MC редактор NANO и почтовый клиент MUTT.
_Если вы не считаете нужным установку этих пакетов, то данный шаг можно пропустить._

`sudo apt-get install mc nano mutt`

**Шаг 2.** Устанавливаем пакеты для сборки платформы Open edX

* `sudo apt-get install -y build-essential software-properties-common python-software-properties curl git-core libxml2-dev libxslt1-dev libfreetype6-dev python-pip python-apt python-dev libxmlsec1-dev swig`

