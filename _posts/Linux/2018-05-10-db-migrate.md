---
layout: post
title: Миграции базы данных в Node.js
---

## Установка
```
npm install db-migrate db-migrate-mysql
```

## Конфиг database.json
```json
{
  "dev": {
    "driver": "mysql",
    "user": <логин>,
    "password": <пароль>
  }
}
```

Можно использовать переменные окружения:

```json
{
  "dev": {
    "driver": "mysql",
    "user": {
      "ENV": "DB_USERNAME"
    },
    "password": {
      "ENV": "DB_PASSWORD"
    }
  }
}
```

При выполнении команд по умолчанию используется окружение `dev` либо `development`.  
Для mysql добавляем `"multipleStatements": true`

```json
{
  "dev": {
    "driver": "mysql",
    ...
    "multipleStatements": true
  }
}
```

## Запуск
```
node_modules/.bin/db-migrate
```

## Создание базы
```
node_modules/.bin/db-migrate db:create <имя базы>
```

## Удаление базы
```
node_modules/.bin/db-migrate db:drop <имя базы>
```

## Создание миграции
Добавляем `database` в `database.json`
```json
{
  "dev": {
    "driver": "mysql",
    ...
    "database": <имя базы>
  }
}
```

```
node_modules/.bin/db-migrate create <имя миграции>
```

Миграции по умолчанию создаются в директории `migrations`

## Создание миграции в формате SQL
```
node_modules/.bin/db-migrate create <имя миграции> --sql-file
```

## Выполнение миграций
После создания миграции необходимо определить `up` и `down` действия для применения и отката миграции соответственно. Для этого редактируем соответствующие файлы в `migrations`, а затем выполняем:

```
node_modules/.bin/db-migrate up
```

либо для применения конкретной миграции:

```
node_modules/.bin/db-migrate up <имя файла миграции без расширения .js>
```

## Откат миграций
Откатить последнюю миграцию

```
node_modules/.bin/db-migrate down
```

Откатить все миграции

```
node_modules/.bin/db-migrate reset
```

## Сайты:
- <https://db-migrate.readthedocs.io/en/latest/>
- <https://github.com/db-migrate/node-db-migrate>
- <https://www.npmjs.com/package/db-migrate>
