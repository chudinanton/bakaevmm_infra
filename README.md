[![Build Status](https://travis-ci.com/Otus-DevOps-2018-09/bakaevmm_infra.svg?branch=master)](https://travis-ci.com/Otus-DevOps-2018-09/bakaevmm_infra)

# bakaevmm_infra
bakaevmm Infra repository
## Homework #3
### Подключаемся к хосту someinternalhost через bastion VM
#### Запускаем ssh агент
```
eval `ssh-agent -s`
```
### Добавляем ключ
```
ssh-add ~/.ssh/id_rsa
```

#### Подключаемся к someinternalhost
```
ssh -A -t user@bastion_host ssh dest_host
```

### Alias ssh_config
Используем ssh конфиг для пользователя. При использование параметра `ForwardAgent` не требуется запущенный на сервере `ssh-agent`.

`~/.ssh/config`
```
Host bastion
 user Bakaev
 Hostname 35.233.71.130
 ForwardAgent yes
Host someinternalhost
 user Bakaev
 Hostname 10.132.0.3
 Port 22
 ProxyJump bastion
```
Параметр `ProxyJump(-J)` возможно использовать начиная с версии `openssh v7.3`. Если использвется верися ниже то возможно использовать параметр `ProxyCommand`.
```
Host bastion
 user Bakaev
 Hostname 35.233.71.130
 ForwardAgent yes
Host someinternalhost
 user Bakaev
 Hostname 10.132.0.3
 Port 22
 ProxyCommand ssh -W  %h:%p bastion
```

### Подключение к vpn pritunl

Для подключения используем клиент openvpn
Адрес панели управления `https://pritunl.35.233.71.130.sslip.io`

### Даныые для проверки
```
bastion_IP = 35.233.71.130
someinternalhost_IP = 10.132.0.3
```



## Homework #4

### Создание vm с использованием startup script
```
gcloud compute instances create reddit-app\
  --boot-disk-size=10GB\
  --image-family ubuntu-1604-lts\
  --image-project=ubuntu-os-cloud\
  --machine-type=g1-small\
  --tags puma-server\
  --restart-on-failure\
  --metadata-from-file startup-script=/app/otus/bakaevmm_infra/startup.sh 
```

### Создание vm с использованием startup script url
#### Создаём GS
```
gsutil mb gs://otus-bakaevmm
gsutil cp /app/otus/bakaevmm_infra/startup.sh gs://otus-bakaevmm
```
#### Создаём vm
```
gcloud compute instances create reddit-app\
  --boot-disk-size=10GB\
  --image-family ubuntu-1604-lts\
  --image-project=ubuntu-os-cloud\
  --machine-type=g1-small\
  --tags puma-server\
  --restart-on-failure\
  --scopes storage-ro\
  --metadata startup-script-url=gs://otus-bakaevmm/startup.sh
```

### Создаём правило firewall
```
gcloud compute firewall-rules create default-puma-server\
  --allow tcp:9292\
  --source-ranges 0.0.0.0/0\
  --target-tags puma-server
```
### Данные для проверки
```
testapp_IP = 35.205.37.42
testapp_port = 9292
```



## Homework #5

* Установлен packer
* Создан шаблон описывающий создание образа ubuntu 16
* Доработан шаблон(в шаблон упакованы все зависимости приложения reddit-app + приложение reddit-app + создан systemd файл для старта puma в качестве сервиса)
```
packer validate -var-file=variables.json  immutable.json
```

## Homework #6

* Создал машины и правила фаервола с исппользованием Terraform
* Параметризировал файл с использованием переменных

### Задания с **

* При добавление ключей в методанные проекта, будут удалены все ключи, которые добавлены через web-интерфейс
* При работе балансировщика у каждого приложения своя БД, что делает балансировку не целесообразной


## Homework #7

* Разделили окружения на stage + prod
* научились использовать модули

### Задания с **

* Использовали модуль storage-bucket для хранения state файлов в удалённом хзранилище
* Проверили работу блокировок(важно для командной работы)

## Homework #8

* Командой ansible app -m command -a 'rm -rf ~/reddit' удаляем git repo
* Плейбуком clone.yml закачиваем репозиторий

### Задания с *

* Добавлен файл inventory.json
* Проверена работоспособность json inventory

## Homework #9

* Написаны ansible playbook для шаблонов пакера.
* Пересобраны шаблоны образов с использованием ansible.
* Созданы плейбуки для настройки приложения и базы данных
* Плейбуки разделены на несколько файлов и объединены одним плейбуком с использованием import_playbook

### Задания с *

* Для получения dynamic inventory используется плагин gcp_compute

## Homework #10

* Созданы роли для настройки инфраструктуры.
* Старые плейбуки пернесены в отдельную директорию.
* Научились работать с ansible-galaxy.
* Роли разделены на stage и prod.
* Для шифрования файлов с "секретами" использованы ansible vault.

### Задание с *

* Настроено использование dynamic inventory.
* Настройка travis не выполнена.

## Homework #11

* Установлены Vagrant, Virtual Box, Molecule. 
* Научился использовать Vagarnt + Molecule для локального тестирования ролей.

### Задание с *

* Роль DB вынесена в отдельный репозиторий.
* Добавлен label со статусом билда.
* Настройка travis для repo DB не выполнена.
