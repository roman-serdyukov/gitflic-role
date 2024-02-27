# Запуск в Kubernetes

---
##### \* Папка с манифестами для kubectl, не доступна в base версии!

Для запуска приложения в kubernetes вам потребуется установить [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/).

> Для теста, приложения можно развернуть в кластере [minikube](https://kubernetes.io/ru/docs/tasks/tools/install-minikube/).
> По умолчанию minikube запускается с 2GB RAM и 2CPU. Рекомендуем  изменить значение на более высокие, например `minikube start --memory=4GB --cpus=max`

Все команды выполняются из директории с бинарником `gitflic` и папкой `kuber`.
```
├── gitflic.jar
├── kuber
├── ...
```

**Запуск подов с redis и postgresql** 

Для начала вам потребуется запустить поды с redis и postgresql.

* Redis:
```
kubectl apply -f kuber/redis/
```

* Postgresql:
```
kubectl apply -f kuber/postgres/
```

**Запуск пода с GitFlic**

Далее соберите образ Gitflic:
```
docker build -t gitflic:latest -f kuber/Dockerfile .
```
Если вы разворачиваете в кластере minikube, то контейнер с созданный локально не будет виден в кластере. Для загрузки docker image выполните следующую команду:
```
minikube image load gitflic:latest
```


* Сконфигурируйте `key.pem` ключ и поместите его данные в `kuber/gitflic/git_config/gitflic-cert.yaml` на строчку ниже знака "`|`".  Инструкцию как сгенерировать ключ key.pem можно посмотреть в [этой статье](https://docs.gitflic.space/setup/setup_and_start#%D0%93%D0%B5%D0%BD%D0%B5%D1%80%D0%B0%D1%86%D0%B8%D1%8F-keypem) Обратите внимание, что при добавлении ключа у всех строк должны быть отступы в четыре пробела.
	Пример `gitflic-cert.yaml`:
```yaml
kind: ConfigMap
metadata:
  name: gitflic-cert
  labels:
    app: gitflic
    environment: gitflic
data:
  # file-like keys
  key.pem: |
    -----BEGIN OPENSSH PRIVATE KEY-----
    _YOURKEY_
    -----END OPENSSH PRIVATE KEY-----
```

* Настройте конфигурационный файл `kuber/gitflic/git_config/gitflic-config.yaml` по инструкции для `application.properties` в [этой статье](https://docs.gitflic.space/setup/setup_and_start#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-applicationproperties).
	Пример `gitflic-config.yaml`:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: gitflic-config
  labels:
    app: gitflic
    environment: stage
data:
  # file-like keys
  application.properties: |
    server.port=8080
    server.address=127.0.0.1
    ssh.server.port=22
    gitflic.base.url=http://localhost:8080
    gitflic.transport.url=http://localhost:8080
    spring.redis.host=redis.default.svc.cluster.local
    spring.redis.port=6379
    spring.datasource.url=jdbc:postgresql://postgres.default.svc.cluster.local:5432/gitflicdb
    spring.datasource.username=gitflic
    spring.datasource.password=gitflic
    ssh.server.cert=/opt/gitflic/var/cert/key.pem
    repository.dir=/opt/gitflic/var/repository/
    image.upload.dir=/opt/gitflic/var/img/
    releases.upload.dir=/opt/gitflic/var/releases/
    cicd.pipeline.dir=/opt/gitflic/var/cicd/
    gitflic.registry.package.dir=/opt/gitflic/var/registry/
    spring.mail.host=imap.mail.ru
    spring.mail.port=465
    spring.mail.username=test@mail.ru
    spring.mail.password=password123
    logging.file.name=/var/log/gitflic/server.log
```

* Примените данные конфигурационные файлы:
```
kubectl apply -f kuber/gitflic/git_config/
```

* Примените конфигурационный файлы для `pv`, `pvc`:
```
kubectl apply -f kuber/gitflic/storage
```

* Сконфигурируйте файл `kuber/gitflic/deployment_srv`, добавив название образа gitflic, и примените его: 
>В качестве примера в него добавлен образ бесплатной сборки с docker hub
```
kubectl apply -f kuber/gitflic/deployment.yaml
```

* Примените конфигурационный файлы для `service`:
```
kubectl apply -f kuber/gitflic/service
```

**Подключение к сервису в `minikube`**

* Проверьте запуск подов командой:
```
kubectl get all
```
* Посмотреть логи запуска приложения можно командой (имя пода скопируйте из списка полученного командой выше):
```
kubectl logs -f enterprise-6688d886f7-8jmrc
```
Успешный запуск сервиса выведет последним сообщением следующую информацию:
```
2023-01-11 12:00:00.000  INFO 1 --- [           main] c.g.onpremise.OnPremiseApplication       : Started OnPremiseApplication in 28.688 seconds (JVM running for 29.443)
```
* Выполните проброс портов командой:
```
kubectl port-forward --namespace default svc/enterprise-http  8080:8080
kubectl port-forward --namespace default svc/enterprise-ssh  1122:22
```


*  Зайдите на сайт по адресу `http://localhost:8080`
Логин/пароль для входа в сервис:
```
adminuser@admin.local
qwerty123
```
* Всё данные хранятся на виртуальной машине на которой поднят minikube. Для просмотра данных зайдите на minikube и посмотрите директории указанные в `pv`
```
minikube ssh
```
* Если вы хотите внести изменение в манифест (кроме изменения image), то для их применения необходимо удалить предыдущий манифест. Ниже приведена команда для удаления поды с deployment:
```
kubectl delete deployment enterprise
```
### Возможные проблемы

* Что бы получить доступ к приложению не из локальной сети , необходимо разрешить прослушивание с любого IP адреса
для этого при пробросе портов необходимо указать ключ --address 0.0.0.0
```
kubectl port-forward --address 0.0.0.0 --namespace default svc/enterprise-http  8080:8080
kubectl port-forward --address 0.0.0.0 --namespace default svc/enterprise-ssh  1122:22
```

* Проблема связанная с PostgreSQL. Проверьте корректность установки расширения `pgcrypto`:
   1 Подключитесь к консоли пода с postgres:
```
kubectl exec -it pod-name bash
```
   например `kubectl exec -it postgres-7668965797-48k9k bash`
   2 Введите команды:
```
psql -v ON_ERROR_STOP=1 --username "gitflic" --dbname "gitflicdb"
```
```
create extension pgcrypto;
```
   Если в процессе создания пода всё отработало корректно то должно появиться сообщение: `ERROR:  extension "pgcrypto" already exists`
