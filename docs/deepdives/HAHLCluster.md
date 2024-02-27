# High Availability/Кластеризация
---

- Для создания кластера HA, все инстансы приложения GitFlic, должны ссылаться на одни и те же базы данных **postgresql**, **redis**, **rabbitmq-server** и **elascitsearch**.
- Необходимо подключить ко всем инстансам единое хранилище статических файлов. 
    Параметры: **`repository.dir`**, **`image.upload.dir`**, **`releases.upload.dir`**, **`cicd.pipeline.dir`**, **`gitflic.registry.package.dir`** в файле **`application.properties`**.
- Все инстансы, должны отдавать один и тот же сертификат ssh сервера. 
    Параметр **`ssh.server.cert`** в файле **`application.properties`**.
