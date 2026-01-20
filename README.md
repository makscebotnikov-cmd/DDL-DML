# Домашнее задание к занятию «Работа с данными (DDL/DML)»  - `Чеботников М.Б.`

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.

---

### Задание 1

#### ОТВЕТ:
1.1. Поднимите чистый инстанс MySQL версии 8.0+. Можно использовать локальный сервер или контейнер Docker.
`Создаём структуру каталогов:`
```
mkdir -p ~/mysql/{data,conf,init}
```

`Создаем docker-compose:`
```
nano /mysql/docker-compose.yml`

version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql-server
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: myrootpassword
    ports:
      - "3306:3306"
    volumes:
      - ./data:/var/lib/mysql
      - ./conf:/etc/mysql/conf.d
      - ./init:/docker-entrypoint-initdb.d
    command: --default-authentication-plugin=mysql_native_password
```

`Подключение к MySQL`
```
docker exec -it mysql-server mysql -u root -p
```

1.2. Создайте учётную запись sys_temp.
`Код: `
```
mysql> CREATE USER 'sys_temp'@'%' IDENTIFIED BY 'pass123';
```

1.3. Выполните запрос на получение списка пользователей в базе данных. (скриншот)
`Код: `
```
mysql> SELECT User, Host FROM mysql.user;
```

<img width="299" height="160" alt="1" src="https://github.com/user-attachments/assets/6d22b709-ed31-4f22-987a-3e4eef3714a6" />


1.4. Дайте все права для пользователя sys_temp.
`Код: `
```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'sys_temp'@'%';
mysql> FLUSH PRIVILEGES;

```

1.5. Выполните запрос на получение списка прав для пользователя sys_temp. (скриншот)
`Код: `
```
mysql> SHOW GRANTS FOR 'sys_temp'@'%';
```

<img width="812" height="500" alt="2" src="https://github.com/user-attachments/assets/8826cbb7-aa11-4708-88b8-7b58cc17ee57" />


1.6. Переподключитесь к базе данных от имени sys_temp.

Для смены типа аутентификации с sha2 используйте запрос:

ALTER USER 'sys_test'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
`Код: `
```
docker exec -it mysql-server mysql -u sys_temp -p
```

1.6. По ссылке https://downloads.mysql.com/docs/sakila-db.zip скачайте дамп базы данных.
`Код: `
```
cd ~/mysql
wget https://downloads.mysql.com/docs/sakila-db.zip
unzip sakila-db.zip
```

1.7. Восстановите дамп в базу данных.
`Код: `
```
docker cp sakila-db/sakila-schema.sql mysql-server:/tmp/
docker cp sakila-db/sakila-data.sql mysql-server:/tmp/
docker exec -i mysql-server mysql -u root -pmyrootpassword < sakila-schema.sql
docker exec -i mysql-server mysql -u root -pmyrootpassword sakila < sakila-data.sql
```

1.8. При работе в IDE сформируйте ER-диаграмму получившейся базы данных. При работе в командной строке используйте команду для получения всех таблиц базы данных. (скриншот)
`Код: `
```
docker exec -it mysql-server mysql -u root -p

mysql> SHOW DATABASES;
mysql> USE sakila;
mysql> SHOW TABLES;
```

<img width="390" height="602" alt="3" src="https://github.com/user-attachments/assets/62cc3b3f-b900-447d-be00-70a7b0f702f0" />


Результатом работы должны быть скриншоты обозначенных заданий, а также простыня со всеми запросами.

---

### Задание 2
Составьте таблицу, используя любой текстовый редактор или Excel, в которой должно быть два столбца: в первом должны быть названия таблиц восстановленной базы, во втором названия первичных ключей этих таблиц. Пример: (скриншот/текст)

Название таблицы | Название первичного ключа
customer         | customer_id
Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

#### ОТВЕТ:

<img width="747" height="529" alt="4" src="https://github.com/user-attachments/assets/668bd3ae-dc71-4ac6-b010-0c0154f20ff8" />


---

Задание 3*
3.1. Уберите у пользователя sys_temp права на внесение, изменение и удаление данных из базы sakila.

3.2. Выполните запрос на получение списка прав для пользователя sys_temp. (скриншот)

Результатом работы должны быть скриншоты обозначенных заданий, а также простыня со всеми запросами.


---
