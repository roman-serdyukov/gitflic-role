# Запуск с помощью Docker Engine

---
[Скачайте](https://gitflic.ru/project/gitflic/gitflic/release/latest) последнюю версию GitFlic self-hosted.

Минимальная версия Docker необходимая для работы **20.04**

После запуска, перейдите по адресу указанному при конфигурировании, и проверьте работоспособность сервиса

**Стандартный пользователь и пароль:**

|Значение|Параметр|
|---|---|
|Почта|adminuser@admin.local|
|Пароль|qwerty123|
### Предварительная установка

**1.** Установите Docker Engine , согласно [официальной инструкции](https://docs.docker.com/engine/install/)

**2.** Создайте директории **repo, img, cicd, cert ,log, registry, releases** на хостируемой машине.
```
for d in cert cicd repo img releases registry; do mkdir -p "/var/gitflic/$d"; done;
```

**3.** Создайте сертификат и поместите его в директорию cert созданную в п. 1*</br>
*\* Имя файла сертификата, должно быть **key.pem***
```
sudo ssh-keygen -t ed25519 -f /opt/gitflic/cert/key.pem
```

### Запуск с помощью Docker-Compose

**1.** Перейдите в папку docker, внутри распакованного архива и [отредактируйте файл ENV](https://docs.gitflic.space/setup/docker/env_prop) в соответствии с вашими настройками.</br>
*\* C описанием параметров ENV файла, можно ознакомиться на [данной странице.](https://docs.gitflic.space/setup/docker/env_prop)*

**2.** Запустите сборку образов и запуск контейнеров
```
sudo docker compose --env-file ./ENV up
```

### Запуск отдельного контейнера

Рекомендуемый контейнер для запуска [openjdk:11.0.14.1-jdk](https://hub.docker.com/layers/library/openjdk/11.0.14.1-jdk/images/sha256-4449cc0f4498abae89db038632e21301f7c3efe7f547cff10f4c6ff8dad5cba7)

* Сконфигурируйте Dockerfile в соответствии с [требованиями стандартного развертывания приложения](https://docs.gitflic.space/setup/gitflic_app/pre_install)
* При конфигурировании контейнера с базой данных postges, не забудьте, что для корректной работы, базе данных необходимо расширение pgcrypto

### Загрузка образов Docker из dr.gitflic.ru:


В случае если у Вас нет доступа к https://hub.docker.com/ вы можете скачать необходимые образы докер с нашего сервера dr.gitflic.ru
```
docker pull dr.gitflic.ru/openjdk:11.0.14.1-jdk
docker pull dr.gitflic.ru/postgres:12
docker pull dr.gitflic.ru/redis:6.2-alpine
```
Так же доступны следующие образы
```
docker pull dr.gitflic.ru/redis:7.0.0
docker pull dr.gitflic.ru/runner-helper
```
