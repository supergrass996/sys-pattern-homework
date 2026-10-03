# Домашнее задание к занятию "Практическое задание с самопроверкой «Базы данных» - `Микрюков Александр`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. В личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

Опишите не менее семи таблиц, из которых состоит база данных. Определите:

какие данные хранятся в этих таблицах,
какой тип данных у столбцов в этих таблицах, если данные хранятся в PostgreSQL.
Начертите схему полученной модели данных. Можете использовать онлайн-редактор: https://app.diagrams.net/

Этапы реализации:

Внимательно изучите предоставленный вам файл с данными и подумайте, как можно сгруппировать данные по смыслу.
Разбейте исходный файл на несколько таблиц и определите список столбцов в каждой из них.
Для каждого столбца подберите подходящий тип данных из PostgreSQL.
Для каждой таблицы определите первичный ключ (PRIMARY KEY).
Определите типы связей между таблицами.
Начертите схему модели данных. На схеме должны быть чётко отображены:
все таблицы с их названиями,
все столбцы с указанием типов данных,
первичные ключи (они должны быть явно выделены),
линии, показывающие связи между таблицами.

`Приведите ответ в свободной форме........`

1. `Разбор исходных данных
В файле одна плоская таблица с 8 колонками. Для нормализации я выделила повторяющиеся сущности (должности, подразделения, филиалы, проекты) в отдельные таблицы, а связь «сотрудник ↔ проекты» реализовала через таблицу-связку, так как один сотрудник может быть назначен на несколько проектов.

Получилось 7 таблиц:

1. employees — сотрудники

employee_id	SERIAL	Первичный ключ
full_name	VARCHAR(100) NOT NULL	ФИО сотрудника
salary	NUMERIC(10,2) NOT NULL	Оклад
hire_date	DATE NOT NULL	Дата найма
position_id	INTEGER NOT NULL	FK → positions
department_id	INTEGER NOT NULL	FK → departments
branch_id	INTEGER NOT NULL	FK → branches

2. positions — должности

position_id	SERIAL	Первичный ключ
title	VARCHAR(100) NOT NULL	Название должности
3. department_types — типы подразделений
Столбец	Тип PostgreSQL	Описание
dept_type_id	SERIAL	Первичный ключ
name	VARCHAR(50) NOT NULL	«Отдел», «Группа», «Департамент»

4. departments — структурные подразделения

department_id	SERIAL	Первичный ключ
name	VARCHAR(150) NOT NULL	Название подразделения
dept_type_id	INTEGER NOT NULL	FK → department_types

5. branches — филиалы

branch_id	SERIAL	Первичный ключ
address	VARCHAR(200) NOT NULL	Адрес филиала

6. projects — проекты

project_id	SERIAL	Первичный ключ
name	VARCHAR(200) NOT NULL	Название проекта

7. employee_projects — связь сотрудников и проектов

employee_id	INTEGER NOT NULL	FK → employees
project_id	INTEGER NOT NULL	FK → projects
Первичный ключ — составной: (employee_id, project_id).

Связи между таблицами
Связь	Тип	Описание
employees → positions	N:1	Многие сотрудники — одна должность
employees → departments	N:1	Многие сотрудники — одно подразделение
departments → department_types	N:1	Многие подразделения — один тип
employees → branches	N:1	Многие сотрудники — один филиал
employees ↔ projects	M:N	Через таблицу employee_projects

```
Поле для вставки кода...

CREATE TABLE department_types (
    dept_type_id SERIAL PRIMARY KEY,
    name         VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    name          VARCHAR(150) NOT NULL,
    dept_type_id  INTEGER NOT NULL REFERENCES department_types(dept_type_id)
);

CREATE TABLE positions (
    position_id SERIAL PRIMARY KEY,
    title        VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE branches (
    branch_id SERIAL PRIMARY KEY,
    address   VARCHAR(200) NOT NULL
);

CREATE TABLE projects (
    project_id SERIAL PRIMARY KEY,
    name       VARCHAR(200) NOT NULL
);

CREATE TABLE employees (
    employee_id   SERIAL PRIMARY KEY,
    full_name     VARCHAR(100) NOT NULL,
    salary        NUMERIC(10,2) NOT NULL,
    hire_date     DATE NOT NULL,
    position_id   INTEGER NOT NULL REFERENCES positions(position_id),
    department_id INTEGER NOT NULL REFERENCES departments(department_id),
    branch_id     INTEGER NOT NULL REFERENCES branches(branch_id)
);

CREATE TABLE employee_projects (
    employee_id INTEGER NOT NULL REFERENCES employees(employee_id),
    project_id  INTEGER NOT NULL REFERENCES projects(project_id),
    PRIMARY KEY (employee_id, project_id)
);


`При необходимости прикрепитe сюда скриншоты
![Скриншот схемы модели данных https://github.com/supergrass996/sys-pattern-homework/blob/917a61ab5b9c82d6538513bf09432b380d81d2ed/img/get_preview_url.png 


---

### Задание 2

Разверните СУБД Postgres на своей хостовой машине, на виртуальной машине или в контейнере docker.
Опишите схему, полученную в предыдущем задании, с помощью скрипта SQL.
Создайте в вашей полученной СУБД новую базу данных и выполните полученный ранее скрипт для создания вашей модели данных.

`Приведите ответ в свободной форме........`

Поле для вставки кода...

-- Скрипт создания схемы БД
CREATE TABLE department_types (
    dept_type_id SERIAL PRIMARY KEY,
    name         VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    name          VARCHAR(150) NOT NULL,
    dept_type_id  INTEGER NOT NULL REFERENCES department_types(dept_type_id)
);

CREATE TABLE positions (
    position_id SERIAL PRIMARY KEY,
    title       VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE branches (
    branch_id SERIAL PRIMARY KEY,
    address   VARCHAR(200) NOT NULL
);

CREATE TABLE projects (
    project_id SERIAL PRIMARY KEY,
    name       VARCHAR(200) NOT NULL
);

CREATE TABLE employees (
    employee_id   SERIAL PRIMARY KEY,
    full_name     VARCHAR(100) NOT NULL,
    salary        NUMERIC(10,2) NOT NULL,
    hire_date     DATE NOT NULL,
    position_id   INTEGER NOT NULL REFERENCES positions(position_id),
    department_id INTEGER NOT NULL REFERENCES departments(department_id),
    branch_id     INTEGER NOT NULL REFERENCES branches(branch_id)
);

CREATE TABLE employee_projects (
    employee_id INTEGER NOT NULL REFERENCES employees(employee_id),
    project_id  INTEGER NOT NULL REFERENCES projects(project_id),
    PRIMARY KEY (employee_id, project_id)
);

`При необходимости прикрепитe сюда скриншоты
![Скриншот диаграммы](https://github.com/supergrass996/sys-pattern-homework/blob/main/img/Diagramma.jpg)

