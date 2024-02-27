# Требования для запуска приложения GitFlic 
---

### Минимальные системные требования:
| Параметр | Рекомендации |
| --- | --- |
| CPU | 2 ядра Ice Lake |
| RAM | 4 Гб |
| HDD | Зависит от объёма ваших репозиториев. От 25 Гб |

### Для запуска приложения нужны следующие компоненты:
 |Компонент | Название пакета | Минимальная версия |
 |---|---|---|
 | Java 11  | openjdk-11-jdk| >= 11.0.18 
 | Redis | redis | >= 6.2.0 |
 | PostgreSQL | postgresql + pgcrypto extension* | >= 11  |
 | RabbitMQ (для enterprise-версии) | rabbitmq-server | >= 3.9 |
 
\*Для корректной работы приложения, требуется расширение [pgcrypto](https://www.postgresql.org/docs/16/pgcrypto.html) для **postgresql** 

[Предварительные условия](https://docs.gitflic.space/setup/gitflic_app/pre_install)</br>
[Установка и запуск в Docker контейнере](https://docs.gitflic.space/setup/docker/docker_setup)</br>
[Установка и запуск в Kubernetes](https://docs.gitflic.space/setup/gitflic_app/kuber_setup)</br>
