# Домашнее задание к занятию "09.03 CI\CD"

## Подготовка к выполнению

1. Создаём 2 VM в yandex cloud со следующими параметрами: 2CPU 4RAM Centos7(остальное по минимальным требованиям)
2. Прописываем в [inventory](./infrastructure/inventory/cicd/hosts.yml) [playbook'a](./infrastructure/site.yml) созданные хосты
3. Добавляем в [files](./infrastructure/files/) файл со своим публичным ключом (id_rsa.pub). Если ключ называется иначе - найдите таску в плейбуке, которая использует id_rsa.pub имя и исправьте на своё
4. Запускаем playbook, ожидаем успешного завершения
5. Проверяем готовность Sonarqube через [браузер](http://localhost:9000)
6. Заходим под admin\admin, меняем пароль на свой
7.  Проверяем готовность Nexus через [бразуер](http://localhost:8081)
8. Подключаемся под admin\admin123, меняем пароль, сохраняем анонимный доступ
<img width="1431" alt="Screen Shot 2022-08-16 at 00 08 05" src="https://user-images.githubusercontent.com/36231585/184718827-061af7bb-fb29-4719-88b9-ff88f974cd99.png">
<img width="1427" alt="Screen Shot 2022-08-16 at 00 08 10" src="https://user-images.githubusercontent.com/36231585/184718850-ebd61aa4-9295-45fb-9917-86961566dfa7.png">


## Знакомоство с SonarQube

### Основная часть

1. Создаём новый проект, название произвольное
2. Скачиваем пакет sonar-scanner, который нам предлагает скачать сам sonarqube
3. Делаем так, чтобы binary был доступен через вызов в shell (или меняем переменную PATH или любой другой удобный вам способ)
4. Проверяем `sonar-scanner --version`
5. Запускаем анализатор против кода из директории [example](./example) с дополнительным ключом `-Dsonar.coverage.exclusions=fail.py`
6. Смотрим результат в интерфейсе
7. Исправляем ошибки, которые он выявил(включая warnings)
8. Запускаем анализатор повторно - проверяем, что QG пройдены успешно
9. Делаем скриншот успешного прохождения анализа, прикладываем к решению ДЗ
<img width="1432" alt="Screen Shot 2022-08-16 at 00 32 05" src="https://user-images.githubusercontent.com/36231585/184722882-449b8694-4223-4615-8f10-f775544fe58f.png">
<img width="1426" alt="Screen Shot 2022-08-16 at 00 34 57" src="https://user-images.githubusercontent.com/36231585/184722895-20b764de-40a2-490c-87b0-81f80b181274.png">

## Знакомство с Nexus

### Основная часть

1. В репозиторий `maven-public` загружаем артефакт с GAV параметрами:
   1. groupId: netology
   2. artifactId: java
   3. version: 8_282
   4. classifier: distrib
   5. type: tar.gz
2. В него же загружаем такой же артефакт, но с version: 8_102
3. Проверяем, что все файлы загрузились успешно
4. В ответе присылаем файл `maven-metadata.xml` для этого артефекта

### Знакомство с Maven

### Подготовка к выполнению

1. Скачиваем дистрибутив с [maven](https://maven.apache.org/download.cgi)
2. Разархивируем, делаем так, чтобы binary был доступен через вызов в shell (или меняем переменную PATH или любой другой удобный вам способ)
3. Удаляем из `apache-maven-<version>/conf/settings.xml` упоминание о правиле, отвергающем http соединение( раздел mirrors->id: my-repository-http-unblocker)
4. Проверяем `mvn --version`
5. Забираем директорию [mvn](./mvn) с pom

### Основная часть

1. Меняем в `pom.xml` блок с зависимостями под наш артефакт из первого пункта задания для Nexus (java с версией 8_282)
2. Запускаем команду `mvn package` в директории с `pom.xml`, ожидаем успешного окончания
3. Проверяем директорию `~/.m2/repository/`, находим наш артефакт
```
beketov@beketovs-MacBook-Pro 8_282 % ls -al
total 128
drwxr-xr-x  6 beketov  staff    192 Aug 16 00:50 .
drwxr-xr-x  3 beketov  staff     96 Aug 16 00:50 ..
-rw-r--r--  1 beketov  staff    175 Aug 16 00:50 _remote.repositories
-rw-r--r--  1 beketov  staff  50327 Aug 16 00:50 java-8_282-distrib.tar.gz
-rw-r--r--  1 beketov  staff     40 Aug 16 00:50 java-8_282-distrib.tar.gz.sha1
-rw-r--r--  1 beketov  staff    394 Aug 16 00:50 java-8_282.pom.lastUpdated
```
4. В ответе присылаем исправленный файл `pom.xml`

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
