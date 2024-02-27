Role Name
=========

Ansible role для быстрого развертывания запуска Gitflic в Docker (с целью тестирования).

Состоит из следующих действий:
- prestart.yml - настройки перед запуском;
- install-docker.yml - установка Docker;
- start.yml - запуск.

!!!После установки изменится порт доступа к хосту по SSH на 2222!!!

Requirements
------------

Работоспособность протестирована на Ubuntu 22.04 и gitflic v3.0.0.

Before Insatll
--------------

Скачать архив с gitflic, распоковать и положить файл gitflic.jar в папку files.

Role Variables
--------------

dir_root - путь для размещения файлов gitflic

Остальные переменные в ./templates/ENV.j2

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```
- name: Install gitlab ci
  hosts: servers
  roles:
    - gitflic-role
```
License
-------

MIT

Author Information
------------------

Сердюков Роман
reserdukov@gmail.com
```