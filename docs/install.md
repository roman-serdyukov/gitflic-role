#  Предварительные условия
---
### Установка зависимостей

Установите зависимые приложения согласно официальным инструкциям.

Ссылки на официальные ресурсы
* [Postgresql](https://www.postgresql.org/)
* [Redis](https://redis.io/)
* [OpenJDK 11](https://openjdk.org/projects/jdk/11/)
* [RabbitMQ](https://www.rabbitmq.com/)

\*Убедитесь что версии пакетов соответствуют необходимым требованиям

### Создание и конфигурирование базы Postgresql

Для корректной работы приложения , необходимо установить расширение [pgcrypto](https://www.postgresql.org/docs/16/pgcrypto.html) в БД с которой планируется использовать приложение.</br>
\* Подробнее с pgcrypto можно ознакомиться на странице [PgCrypto for Postgresql](https://www.postgresql.org/docs/16/pgcrypto.html)

##### Ubuntu, debian-based и РЕД ОС

**1.** Зайдите под пользователем PostgreSQL:</br>
*\*Для выхода введите команду exit*
```
sudo -i -u postgres
``` 
**2.** Создайте пользователя и базу данных:
```
createuser gitflic && createdb gitflic
```
**3.** Войдите в базу данных:
*\*Для выхода введите команду \q*
```
psql --dbname "gitflic"
```
**4.** Задайте пароль для пользователя созданного в п.2:</br>
*\* Пароль должен быть заключен в одинарные кавычки **' '***</br>
*\* Комманды в psql должны заканчиваться символом **;***
```
alter user gitflic with password 'gitflic';
```
**5.** Установите расширение pgcrypto:
```
create extension pgcrypto;
```

### Конфигурация SSH порта

Для того, чтобы было возможным использовать remote-url вида `git@gitflic.ru:gitflic/gitflic.git`  , стандартный 22 порт ssh должен быть свободен , так как приложение запускает собственный ssh сервер на данном порту. 

Как изменить стандартный ssh порт в системе и/или в приложении см. на [данной странице.](https://docs.gitflic.space/setup/ssh_port_setup)


### Конфигурация SMTP сервера

Для пробного запуска приложения, обязательная конфигурация SMTP сервера не требуется. Новых пользователей можно создать через панель администратора.

*\*При необходимости, SMTP сервер можно будет сконфигурировать и подключить позднее*

В качестве пробного примера можно использовать один из бесплатных почтовых серверов с возможностью предоставления SMTP протокола

* [Yandex](https://yandex.ru/support/mail/mail-clients/others.html)
* [Mail.ru](https://help.mail.ru/mail/mailer/popsmtp)

[Минимальные требования](https://docs.gitflic.space/setup/gitflic_app/min_req)</br>
[Установка приложения GitFlic](https://docs.gitflic.space/setup/gitflic_app/install)
# Установка приложения GitFlic 
---

[Скачайте](https://gitflic.ru/project/gitflic/gitflic/release/latest) последнюю версию GitFlic self-hosted.

### Установка через install\.sh

\* Доступно в комплектах поставки версии 3.0.0 и выше

**Запустите программу установки**

*\* Команда выполняется из папки, в которую было распаковано приложение*</br>
*\* Добавьте ключ -q что бы убрать интерактивный режим*
```
sudo ./gitflic.sh install
```
**Структура и расположение файлов:**

`/var/gitflic/` - Статические файлы пользователей</br>
`/opt/gitflic/` - Файлы приложения</br>
`/etc/gitflic/` - Файлы настроек приложения</br>
`/var/log/gitflic/` - Файлы логов приложения


### Установка в ручном режиме 

Для работы приложения, вам потребуется создать рабочие директории для следующих типов файлов:
- Пользовательские изображения
- Репозитории git
- Файлы релизов 
- Файлы Реестра Пакетов
- Артефакты cicd

**1**. Создайте директории для хранения статических файлов.*
```
for d in cicd repo img releases registry; do sudo mkdir -p "/var/gitflic/$d"; done;
```

**2.** Создайте директорию для размещения приложения*
```
sudo mkdir -p /opt/gitflic
```

**3.** Создайте директорию для размещения настроек приложения*
```
sudo mkdir -p /etc/gitflic
```

**4.** Создайте директорию для размещения логов приложения*
```
sudo mkdir -p /var/log/gitflic
```

**5.** Скопируйте файл **gitflic.jar** в директорию созданную в п. 2
*\* Команда выполняется из директории, в которую было распаковано приложение*
```
sudo cp gitflic.jar /opt/gitflic/
```
**6.** Скопируйте файл со стандартными найстройками в директорию созданную в п. 3**</br>
*\* Команда выполняется из директории, в которую было распаковано приложение*</br>
*\* В версиях ниже 3.0.0 ,файл **application.properties** находится в директории default-config*
```
sudo cp application.properties /etc/gitflic/application.properties
```


\* Данные директории можно размещать в удобном для вас месте. В таком случае будет необходимо переопределить соответствующий параметр в настройках приложения в файле application.properties.

\*\* Имя файла настроек должно быть **application.properties**

[Предварительные условия](https://docs.gitflic.space/setup/gitflic_app/pre_install)</br>
[Конфигурирование и запуск приложения GitFlic ](https://docs.gitflic.space/setup/gitflic_app/post_install)
# Конфигурирование и запуск приложения GitFlic 
---

Перед конфигурацией приложения, ознакомьтесь с назначением параметров на странице [Конфигурация application.properties](https://docs.gitflic.space/setup/gitflic_app/app_prop)

После запуска перейдите по адресу указанному при конфигурировании и проверьте работоспособность сервиса

**Стандартный пользователь и пароль:**

|Значение|Параметр|
|--------|--------|
|Почта|adminuser@admin.local|
|Пароль|qwerty123|


### Создание ключа для ssh сервера

**1.** Создайте директорию для хранения сертификата</br>
*\* Данную директорию можно размещать в удобном для вас месте. В таком случае будет необходимо переопределить соответствующий параметр в настройках приложения в файле application.properties.*
```
sudo mkdir -p /opt/gitflic/cert;
```

**2.** Создайте сертификат и поместите его в директорию созданную в п.1</br>
*\* Имя файла сертификата должно быть key.pem*
```
sudo ssh-keygen -t ed25519 -f /opt/gitflic/cert/key.pem
```

### Конфигурация через gitflic\.sh configure

*\* Доступно в версии 3.0.0 и выше*</br>
*\* Команда выполняется из папки, в которую было установлено приложение*

Запустите программу конфигурации и следуйте инструкциям на экране
```
sudo ./gitflic.sh configure
```

\*Используйте ключи при запуске команды что бы правильно сконфигурировать приложение. Без указания ключей будут применены настройки по умолчанию.

|Ключ|Значение|Значение по умолчанию|Описание|
|----|--------|---------------------|--------|
|-m|ent, full, base|base| Указание версии приложения <br> ent - enterprise-medium , full - enterprise full , base - base (OnPremise)|
|-c|Путь до файла конфигурации|/etc/gitflic/|Указание нестандартного пути до файла конфигурации.</br>Значение должно заканчиваться на / |
|-nit| | |Ключ указывающий на пропуск интерактивного режима установки.</br>Будет формирован конфигурационный файл со стандартными настройками|
|-smtp| | |Ключ указывающий, что необходимо сконфигурировать настройки SMTP сервера|

### Конфигурация в ручном режиме 

Отредактируйте файл  **application.properties**, скопированный раннее при установке, в соответствии с [описанием конфигурационных параметров.](https://docs.gitflic.space/setup/gitflic_app/app_prop)


\* Все параметры, помеченные как **обязательные**, должны быть определены в файле.

### Запуск через gitflic\.sh start

Запустите программу gitflic.sh с параметром start*
*\*Команда выполняется из папки, в которую было установлено приложение*
```
sudo ./gitflic.sh start
```
```
sudo ./gitflic.sh start -c <путь до файла с настройками>
```

\* Используйте ключи при запуске команды что бы правильно сконфигурировать приложение. Без указания ключей будут примены настройки по умолчанию.

|Ключ|Параметр по умолчанию|Описание|
|----|---------------------|--------|
|-c|/etc/gitflic/|Указание нестандартного пути до файла конфигурации.<br>Значение должно заканчиваться на / |
|-j| ./ |Указание пути до файла gitflic.jar <br>Значение должно заканчиваться на / |
|-mmx| |Максимальное количество выделяемой оперативной памяти для JVM|

C особенностями JVM и параметром **-mmx**, можно ознакомиться на [данной странице.](https://docs.oracle.com/cd/E15289_01/JRCLR/optionx.htm#i1027399)

### Запуск в ручном режиме
Выполните команду 
*\* Команда выполняется из папки, в которую было установлено приложение*
```
java -jar gitflic.jar --spring.config.additional-location=file:/etc/gitflic/
```
\* Параметр `--spring.config.additional-location=file:` - Указание пути до файла с настройками приложения. Путь должен заканчиваться на `/`.
`/etc/gitflic/` - Директория по умолчанию.

C особенностями JVM и параметрами запуска приложений, можно ознакомиться на [данной странице.](https://docs.oracle.com/cd/E15289_01/JRCLR/optionx.htm)
</br>
</br>
[Установка приложения GitFlic](https://docs.gitflic.space/setup/gitflic_app/install)