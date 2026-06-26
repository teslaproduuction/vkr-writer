# Руководство по созданию UML-диаграмм для ВКР

Диаграммы генерируются через PlantUML (нужна Java). Команда:
```bash
java -jar plantuml.jar -charset UTF-8 -tpng -Sdpi=300 файл.puml -o "./output"
```

Параметр `-Sdpi=300` даёт разрешение 2000-4000px -- достаточно для печати.

---

## Оглавление

1. [Общие настройки skinparam](#1-общие-настройки)
2. [Диаграмма вариантов использования](#2-диаграмма-вариантов-использования)
3. [Диаграмма последовательности](#3-диаграмма-последовательности)
4. [Диаграмма деятельности](#4-диаграмма-деятельности)
5. [Диаграмма классов](#5-диаграмма-классов)
6. [Диаграмма развёртывания](#6-диаграмма-развёртывания)
7. [Концептуальная модель БД](#7-концептуальная-модель-бд)
8. [Логическая модель БД](#8-логическая-модель-бд)
9. [Диаграмма архитектуры](#9-диаграмма-архитектуры)

---

## 1. Общие настройки

Все диаграммы должны начинаться с этих skinparam для единообразного вида:

```plantuml
skinparam backgroundColor white
skinparam defaultFontName "Times New Roman"
skinparam defaultFontSize 12
```

---

## 2. Диаграмма вариантов использования

Правила:
- Только ОДИН актор -- Пользователь (внешние системы не показываются)
- Пользовательские прецеденты: действия, которые инициирует пользователь
- Системные прецеденты: автоматические процессы внутри системы
- Связи: `<<include>>` для обязательного включения, `<<extend>>` для опционального расширения
- Направление: `left to right direction` для горизонтальной компоновки

```plantuml
@startuml
skinparam backgroundColor white
skinparam defaultFontName "Times New Roman"
skinparam defaultFontSize 12
skinparam packageStyle rectangle

left to right direction

actor "Пользователь" as user

rectangle "Название системы" {
  usecase "Действие 1" as UC1
  usecase "Действие 2" as UC2
  usecase "Действие 3" as UC3

  usecase "Системный\nпроцесс 1" as SYS1
  usecase "Системный\nпроцесс 2" as SYS2
}

user --> UC1
user --> UC2
user --> UC3

UC2 ..> SYS1 : <<include>>
SYS1 ..> SYS2 : <<extend>>
@enduml
```

---

## 3. Диаграмма последовательности

Правила:
- Участники: компоненты системы + внешние сервисы
- Стрелки: `->` синхронный вызов, `->>` асинхронный (fire-and-forget)
- `activate`/`deactivate` для показа времени жизни
- `alt`/`else`/`end` для ветвлений
- `note right` для пояснений

```plantuml
@startuml
skinparam backgroundColor white
skinparam defaultFontName "Times New Roman"
skinparam defaultFontSize 12

participant "Компонент A" as a
participant "Компонент B" as b
participant "База данных" as db

a -> b : вызов метода()
activate b
b -> b : внутренняя обработка
note right: Пояснение\nк процессу

alt Условие выполнено
    b -> db : сохранить()
    b --> a : результат
else Условие не выполнено
    b --> a : ошибка
end
deactivate b
@enduml
```

---

## 4. Диаграмма деятельности

Правила:
- `start`/`stop` для начала/конца
- `:Действие;` для шагов
- `if (Условие?) then (да) / else (нет)` для ветвлений
- `fork`/`fork again`/`end fork` для параллельных процессов
- `repeat`/`repeat while` для циклов
- `floating note right` для примечаний

```plantuml
@startuml
skinparam backgroundColor white
skinparam defaultFontName "Times New Roman"
skinparam defaultFontSize 11

start

:Шаг 1;

if (Условие?) then (да)
  :Шаг 2а;
else (нет)
  :Шаг 2б;
  stop
endif

fork
  :Параллельный\nпроцесс 1;
fork again
  :Параллельный\nпроцесс 2;
end fork

repeat
  :Действие в цикле;
repeat while (Продолжать?) is (да)
->нет;

stop
@enduml
```

---

## 5. Диаграмма классов

Правила:
- Не более 5-8 классов для читаемости
- Формат: имя класса, атрибуты (- private, + public), методы
- Связи: `*--` композиция, `o--` агрегация, `..>` зависимость
- Кардинальность: `"1" *-- "0..*"`

```plantuml
@startuml
skinparam backgroundColor white
skinparam defaultFontName "Times New Roman"
skinparam defaultFontSize 11
skinparam classFontSize 12
skinparam classAttributeFontSize 10

class ClassName {
  - attribute1: type
  - attribute2: type
  + method1(): returnType
  + method2(param): returnType
  - _privateMethod()
}

class AnotherClass {
  - data: type
  + doSomething()
}

ClassName "1" *-- "0..*" AnotherClass : contains
ClassName ..> SomeService : uses
@enduml
```

---

## 6. Диаграмма развёртывания

Правила:
- `node` для серверов/устройств
- `component` для программных компонентов
- `database` для баз данных
- `cloud` для внешних сервисов
- Указывать протоколы на связях (HTTP, WebSocket, TCP, HTTPS)

```plantuml
@startuml
skinparam backgroundColor white
skinparam defaultFontName "Times New Roman"
skinparam defaultFontSize 11

node "Сервер" {
  component [Веб-сервер] as web
  component [Приложение] as app
  database "БД" as db
  web --> app
  app --> db
}

node "Клиент" as client {
  component [Браузер] as browser
}

cloud "Внешние сервисы" {
  component [API] as api
}

client -down-> web : HTTP + WebSocket
app --> api : HTTPS
@enduml
```

---

## 7. Концептуальная модель БД

Правила:
- Сущности с НАЗВАНИЕМ НА РУССКОМ и 2-3 ключевыми атрибутами (не все)
- Связи с кардинальностью ("1" -- "0..*")
- Жёлтый фон для сущностей (BackgroundColor #FEFECE)
- Никаких типов данных

```plantuml
@startuml
skinparam backgroundColor white
skinparam defaultFontName "Times New Roman"
skinparam defaultFontSize 12
skinparam linetype ortho

skinparam class {
  BackgroundColor #FEFECE
  BorderColor #A08020
  FontSize 13
}

class "Пользователь\n(User)" as user {
  Логин
  Пароль
  Роль
}

class "Заказ\n(Order)" as order {
  Дата
  Сумма
  Статус
}

class "Товар\n(Product)" as product {
  Название
  Цена
}

user "1" -- "0..*" order
order "1" -- "1..*" product
@enduml
```

---

## 8. Логическая модель БД

Правила:
- Все атрибуты сущности
- PK и FK отмечены жирным
- Разделитель `--` между ключами и остальными атрибутами
- Связи с кардинальностью и типом (композиция `--o`)

```plantuml
@startuml
skinparam backgroundColor white
skinparam defaultFontName "Times New Roman"
skinparam defaultFontSize 10
skinparam linetype ortho

skinparam class {
  BackgroundColor #FEFECE
  BorderColor #A08020
  FontSize 11
  AttributeFontSize 10
}

class "User" as user {
  **PK** id
  --
  username
  password
  email
  role
  created_at
}

class "Order" as order {
  **PK** id
  **FK** user_id
  --
  total
  status
  created_at
}

user "1" --o "0..*" order
@enduml
```

---

## 9. Диаграмма архитектуры

Правила:
- `package` для группировки слоёв
- `component` для модулей
- `database` для хранилищ
- `cloud` для внешних сервисов
- Стрелки показывают направление зависимостей

```plantuml
@startuml
skinparam backgroundColor white
skinparam defaultFontName "Times New Roman"
skinparam defaultFontSize 11
skinparam packageStyle rectangle

package "Веб-слой" {
  [Views] as views
  [Templates] as templates
  [WebSocket] as ws
}

package "Бизнес-логика" {
  [Сервис 1] as s1
  [Сервис 2] as s2
}

database "БД" as db
cloud "Внешние API" {
  [API 1] as api1
}

views --> s1
ws --> s1
s1 --> db
s1 --> api1
@enduml
```

---

## Физическая модель БД

Физическая модель описывается НЕ диаграммой, а таблицами в тексте документа.

Формат таблицы для Django-проектов:

| Имя поля | Тип данных | Обязательность | Прочие ограничения |
|----------|-----------|----------------|-------------------|
| id | AutoField | Да | Первичный ключ |
| name | CharField | Да | max_length=100 |
| email | EmailField | Нет | unique=True |
| role_id | ForeignKey | Да | Внешний ключ на Role |

Формат таблицы для SQL-проектов:

| Название столбца | Тип данных | Ограничения | Описание |
|-----------------|-----------|-------------|---------|
| id | INTEGER | PRIMARY KEY, AUTOINCREMENT | Идентификатор |
| name | VARCHAR(100) | NOT NULL, UNIQUE | Имя |
| created_at | DATETIME | NOT NULL | Дата создания |

Шаблонная фраза перед каждой таблицей (повторять дословно):
> "В таблице N обозначены ограничения и определены типы данных атрибутов сущности, что обозначена в логическом проектировании."
