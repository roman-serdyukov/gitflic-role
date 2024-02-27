
# Переход c версии отличной от 3.х.х


Начиная с версии 3.0.0 вместе со сборкой поставляется `cli.jar` и `gitflic-cli.sh` для запуска.

Описанные ниже команды необходимо запускать в том случае, если вы переходите на версию 3.x.x. 

Случаи когда **НЕОБХОДИМО** запустить `gitflic-cli.sh <путь до application.properties>`:
- Если вы переходите на версию 3.x.x. Например, вы работаете с версией 2.16.1 и у вас появилась необходимость обновить GitFlic до версии 3.0.0 (и выше)

Случаи когда запускать `gitflic-cli.sh` **НЕ НУЖНО:** 
- Вы впервые разворачиваете GitFlic версии 3.0.0+
- Вы ранее уже запускали `gitflic-cli.sh`. Повторно запускать его не требуется. 

Пошаговая инструкция по запуску: 

1. Запускаем скрипт `gitflic-cli.sh <путь до application.properties>`. Например, `gitflic-cli.sh /user/admin/gitflic/default-config/application.properties`

2. Дожидаемся запуска

3. Вводим команду `fix releases`, после чего в консоли будет следующий лог в начале: 

    ```
    2024-02-14 14:37:50.291  INFO 70237 --- [           main] c.gitflic.cli.service.ReleaseFixService  : --- Подсчет количества релизов --- 
    2024-02-14 14:37:50.457  INFO 70237 --- [           main] c.gitflic.cli.service.ReleaseFixService  : Всего релизов 24
    2024-02-14 14:37:50.458  INFO 70237 --- [           main] c.gitflic.cli.service.ReleaseFixService  : ----------------------------------------- 
    
    ```

    Лог после выполнения: 

    ```
    2024-02-14 14:37:51.004  INFO 70237 --- [           main] c.gitflic.cli.service.ReleaseFixService  : Мигрировано: 24/24 100,00% релизов
    2024-02-14 14:37:51.004  INFO 70237 --- [           main] c.gitflic.cli.service.ReleaseFixService  : Задача выполнена
    ```
4. Вводим команду `fix lfs`, после чего в консоли будет следующий лог в начале:

    ```
    2024-02-14 14:49:25.372  INFO 70237 --- [           main] com.gitflic.cli.service.LfsFixService    : --- Подсчет количества проектов с лфс --- 
    2024-02-14 14:49:25.610  INFO 70237 --- [           main] com.gitflic.cli.service.LfsFixService    : Всего проектов с лфс: 12
    ``` 

    Лог после выполнения: 

    ```
    2024-02-14 14:49:25.624  INFO 70237 --- [           main] com.gitflic.cli.service.LfsFixService    : Починено:  12/12 100,00% проектов с lfs
    2024-02-14 14:49:25.625  INFO 70237 --- [           main] com.gitflic.cli.service.LfsFixService    : Задача выполнена
    ```

5. После успешного выполнения можно выйти из приложения
# Процесс обновления GitFlic

---
[Скачайте](https://gitflic.ru/project/gitflic/gitflic/release/latest) последнюю версию GitFlic self-hosted.

Ниже описано, какие дополнительные настройки на сервере надо произвести при переходе на новую версию. Все настройки расписаны под версией продукта. Если для новой версии gitflic нет дополнительной информации, то для обновления достаточно заменить старый gitflic.jar на новый.

### Обновление сервиса настроенного через [systemctl](https://docs.gitflic.space/setup/autorun)
Пример обновления, если запуск приложения настроен через [systemctl](https://docs.gitflic.space/setup/autorun):
```
sudo systemctl stop gitflic
# Меняем старый jar файл на новый.
cp gitflic.jar /opt/gitflic/
sudo systemctl restart gitflic
# Следим за логом запуска.
tail -f /opt/gitflic/logs/server.log
```
### Обновление сервиса запущенного через docker compose
Вам необходимо собрать новый `docker-gitflic` image. Для этого разместите файл `gitflic.jar` рядом с папкой docker в которой размещён `docker-compose.yml` 
```
├── docker
│   ├── default-config
│   ├── docker-compose.yml
│   ├── gitflic
│   ├── postgres
│   └── redis
│
│# Файл gitflic.jar с новой версией gitflic
│
├── gitflic.jar
```
Остановите запущенный `docker compose`. Для этого в папке с файлом `docker-compose.yml` выполните команду:
```
# В windows и macOS не нужно использовать sudo
sudo docker compose down
```
В паке  с файлом `docker-compose.yml` запустите `docker compose` с ключом `--build` для обновления сборки:
```
# В windows и macOS не нужно использовать sudo
sudo docker compose up --build
```


### v 2.14.0
#### Общее обновление для всех версий
Добавился новый функционал "Реестр пакетов". Для его работы необходимо добавить новый параметр в `application.properties` и создать папку для хранения пакетов. 
```
mkdir -p /opt/gitflic/var/registry
echo "#Параметр gitflic.registry.package.dir отвечает за путь для файлов реестра пакетов" >> /opt/gitflic/default-config/application.properties
echo "gitflic.registry.package.dir=/opt/gitflic/var/registry/" >> /opt/gitflic/default-config/application.properties
```
#### Дополнительное обновление для платной версии [self-hosted enterprise](https://gitflic.ru/price)
Для платной версии так же необходимо установить сервис RabbitMQ. Инструкцию по установке можно посмотреть [здесь](https://docs.gitflic.space/setup/setup_and_start#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0-rabbitmq).
Для дополнительных настроек RabbitMQ необходимо добавить дополнительные параметры в `application.properties`
```
# ======= Конфигурация RabbitMQ =======

#spring.rabbitmq.host=localhost
#spring.rabbitmq.port=5672
#spring.rabbitmq.username=
#spring.rabbitmq.password=

#Дополнительные настройки для соединения с RabbitMq можно посмотреть по ссылке
# https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#application-properties.integration.spring.rabbitmq.addresses
```

## Перенос данных GitFlic на другой сервер.

### Общая информация

После настройки GitFlic на новом месте вам необходимо перенести следующие данные:
* База данных из postgresql
* Все папки со статичными файлами. Со списком папок можно ознакомиться [здесь](https://docs.gitflic.space/setup/setup_and_start#%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B8-%D0%B4%D0%B8%D1%80%D0%B5%D0%BA%D1%82%D0%BE%D1%80%D0%B8%D0%B9-%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D1%83%D0%B5%D0%BC%D1%8B%D1%85-%D0%B4%D0%BB%D1%8F-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B-%D0%BF%D1%80%D0%B8%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F). Обратите внимание на то, что у вас на сервере могут быть другие пути, актуальные пути можно посмотреть в application.properties.
* База данных из Redis. У нас происходит сохранение следующих данных на диск: вебхуки, токены, адреса email для оповещений, данные для оповещению в telegram.

### Данные Docker compose

Если вы запустили приложение в docker compose по [данной инструкции](https://docs.gitflic.space/setup/docker_setup). То для переноса данных на новое место вам необходимо скопировать следующие файлы.
```
│
│# Если вы производили дополнительные настройки в файле default-config, то при переезде рекомендую заполнить данный файл заново, так как в новой версии могли появиться новые поля.
│Обратите внимание, что application.properties копируется в image и для его изменения нужно при запуске добавить параметр --build.
│
├── default-config
│   └── application.properties
├── docker-compose.yml
│
│# Если вы производили дополнительные настройки в файле ENV, то при переезде рекомендую заполнить данный файл заново, так как в новой версии могли появиться новые поля.
│
├── ENV
├── gitflic
│   │
│   │ # Перенесите папку data
│   │
│   ├── data
│   │   ├── cert
│   │   │   └── key.pem
│   │   ├── cicd
│   │   ├── img
│   │   ├── log
│   │   ├── registry
│   │   ├── releases
│   │   └── repo
│   └──  Dockerfile
├── postgres
│   │
│   │# Перенесите папку data
│   │
│   ├── data 
│   ├── Dockerfile
│   └── pgcrypto.sh
│
│# Перенесите папку redis
│
└── redis
    └── data
```