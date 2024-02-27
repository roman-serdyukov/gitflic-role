# Включение нативной поддержки TLS/SSL
---

\* Для запуска приложения с нативной поддержкой [TLS](https://ru.wikipedia.org/wiki/TLS)/[SSL](https://ru.wikipedia.org/wiki/SSL) протокола шифрования, потребуется утилита [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) поставляемая вместе с [openjdk](https://www.oracle.com/cis/java/technologies/downloads/).
\* Если при установке JDK/JVM утилита [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) не была установлена, её потребуется установить отдельно.
\* Так же необходимо установить утилиту [openssl](https://www.openssl.org/), для работы с [сертификатами](https://ru.wikipedia.org/wiki/SSL).
\* Для [самозаверенных сертификатов](https://ru.wikipedia.org/wiki/%D0%A1%D0%B0%D0%BC%D0%BE%D0%B7%D0%B0%D0%B2%D0%B5%D1%80%D0%B5%D0%BD%D0%BD%D1%8B%D0%B9_%D1%81%D0%B5%D1%80%D1%82%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%82), потребуется дополнительно установить корневой сертификат [центра сертификации](https://ru.wikipedia.org/wiki/%D0%A6%D0%B5%D0%BD%D1%82%D1%80_%D1%81%D0%B5%D1%80%D1%82%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D0%B8).


**1.** С помощью утилиты [openssl](https://www.openssl.org/) из ваших пары Сертификат/Приватный ключ, необходимо создать  специальное хранилище ключей для jvm и поместить в него информацию о сертификате.</br>
*\* После ввода команды, необходимо будет задать пароль для хранилища ключей. Запомните его, он потребуеться далее!*</br>
*\* Для конвертации форматов сертификата отличных от PEM, воспользуйтесь [справкой по утилите openssl](https://www.openssl.org/docs/manpages.html)*
```
openssl pkcs12 -export -name <alias> -in <cert.crt> -inkey <cert.key> -out <keystore.p12>
``` 

|Параметр|Значение|
|---|---|
|pkcs12|Тип хранилища ключей jvm.</br>*Рекомендуется использовать [pkcs12](https://ru.wikipedia.org/wiki/PKCS12)*|
|\<alias>|Псевдоним, по которому будет осуществляться поиск в хранилище|
|\<cert.crt>|Путь до файла сертификата в формате [PEM](https://ru.wikipedia.org/wiki/%D0%9F%D0%BE%D1%87%D1%82%D0%B0_%D1%81_%D0%BF%D0%BE%D0%B2%D1%8B%D1%88%D0%B5%D0%BD%D0%BD%D0%BE%D0%B9_%D1%81%D0%B5%D0%BA%D1%80%D0%B5%D1%82%D0%BD%D0%BE%D1%81%D1%82%D1%8C%D1%8E#%D0%A1%D0%B5%D1%80%D1%82%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%82%D1%8B)| 
|\<cert.key>|Путь до файла приватного ключа в формате [PEM](https://ru.wikipedia.org/wiki/%D0%9F%D0%BE%D1%87%D1%82%D0%B0_%D1%81_%D0%BF%D0%BE%D0%B2%D1%8B%D1%88%D0%B5%D0%BD%D0%BD%D0%BE%D0%B9_%D1%81%D0%B5%D0%BA%D1%80%D0%B5%D1%82%D0%BD%D0%BE%D1%81%D1%82%D1%8C%D1%8E#%D0%A1%D0%B5%D1%80%D1%82%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%82%D1%8B)|
|\<keystore.p12>|Имя хранилища ключей jvm|

**2.** С помощью утилиты [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) добавьте корневой сертификат [центра сертификации](https://ru.wikipedia.org/wiki/%D0%A6%D0%B5%D0%BD%D1%82%D1%80_%D1%81%D0%B5%D1%80%D1%82%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D0%B8) в хранилище доверенных ключей jvm.</br>
*\* Если используется не самозаверенный сертификат, данный пункт можно пропустить. Достаточно установить в систему стандартные ca-сертификаты*

```
keytool -importcert -alias <rootCA> -keystore /usr/lib/jvm/java-11-openjdk-amd64/lib/security/cacerts -storepass changeit -file <rootCA.crt>
```
|Праметр|Значение|
|---|---|
|\<rootCA>|Псевдоним, по которому будет осуществляться поиск в хранилище|
|/usr/lib/jvm/java-11-openjdk-amd64/lib/security/cacerts|Стандартный путь до хранилища доверенных сертификатов в jvm, при установке пакета [openjdk-11-jdk](https://openjdk.org/projects/jdk/11/).</br> *Каталог **java-11-openjdk-amd64** может быть другим, в зависимости от типа архитектуры системы*|
|changeit|Стандартный пароль для хранилища доверенных сертификатов jvm| 
|\<rootCA.crt>|Путь до файла сертификата в формате [PEM](https://ru.wikipedia.org/wiki/%D0%9F%D0%BE%D1%87%D1%82%D0%B0_%D1%81_%D0%BF%D0%BE%D0%B2%D1%8B%D1%88%D0%B5%D0%BD%D0%BD%D0%BE%D0%B9_%D1%81%D0%B5%D0%BA%D1%80%D0%B5%D1%82%D0%BD%D0%BE%D1%81%D1%82%D1%8C%D1%8E#%D0%A1%D0%B5%D1%80%D1%82%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%82%D1%8B)|

**3.** Откройте файл [application.properties](https://docs.gitflic.space/setup/gitflic_app/app_prop) приложения GitFlic и внесите конфигурационые параметры в соответствии с вашими данными

**\* Параметры отмеченные как обязательные, должны присутствовать в файле!**

|Параметр|Обязательный|Значение|Описание|
|--------|------------|--------|--------|
|server.ssl.enabled|да|true|Включение поддержки ssl|
|server.ssl.protocol|нет|TLS|Указание что необходимо использовать тип протокола TLS|
|server.ssl.key-store|да|file:\<path>|Абсолютный путь до файла хранилища ключей созданного в п.1|
|server.ssl.key-store-type|да|pkcs12|Указание типа хранилища ключей|
|server.ssl.key-store-password|да|\<secret>|Пароль от хранилища ключей созданного в п.1|
|server.ssl.key-alias|да|\<alias>|Псевдоним пары сертификат/ключ указанный в п.1|
|server.ssl.key-password|нет|\<secret>|Пароль от пары сертификат/ключ внутри хранилища ключей jvm созданного в п.1,если таковой был задан.</br>По умолчанию, пароль не установлен|
|server.ssl.trust-store|нет|file:\<path>|Абсолютный путь до файла хранилища ключей доверенных сертификатов jvm, если используется нестандартное хранилище|
|server.ssl.trust-store-password|нет|\<secret>|Пароль от хранилища ключей доверенных сертификатов jvm, если используется нестандартное хранилище|
|server.ssl.trust-store-type|нет|pkcs12/jks|Тип хранилища ключей доверенных сертификатов jvm|

\* С полным списком всех доступных параметров для конфигурации tls/ssl, можно ознакомиться на [данной странице.](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.server)

**4.** Запустите приложение GitFlic. Веб интерфейс будет доступен по адресу https://\<server.address>:\<server.port>
