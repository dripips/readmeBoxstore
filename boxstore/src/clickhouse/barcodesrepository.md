---
description: BarcodesRepository — универсальный репозиторий для справочных таблиц
icon: php
---

# BarcodesRepository

## 📦 BarcodesRepository — универсальный репозиторий для справочных таблиц

Файл: src/ClickHouse/BarcodesRepository.php\
Назначение: работа с таблицами типа typeBox, typeCardboard, colorCardboard и т.п., содержащими поля id (UUID) и value (String).

***

### 📌 Что делает этот класс?

* 🧾 Управляет записями в справочных таблицах ClickHouse
* ✅ Позволяет добавлять, получать, обновлять и удалять записи по UUID
* 🔒 Гарантирует, что используются только разрешённые таблицы

***

### ⚙️ Подключение

```php
$config = [
    'host' => 'localhost',
    'port' => 8123,
    'users' => [
        'write' => [
            'username' => 'user',
            'password' => 'pass'
        ]
    ]
];

$repo = new \ClickHouse\BarcodesRepository($config, 'typeBox');
```

***

### 🛠️ Таблицы, с которыми можно работать

Разрешённые таблицы:

* typeBox
* typeCardboard
* colorCardboard
* sizeBox
* amount

Структура таблиц:

```sql
CREATE TABLE typeBox (
    id    UUID,
    value String
)
ENGINE = MergeTree()
ORDER BY id;
```

***

### 🧰 Методы

➕ create(string $id, string $value): bool

Добавляет новую запись:

```php
$repo->create('550e8400-e29b-41d4-a716-446655440000', 'Коробка №1');
```

🔎 getById(string $id): ?array

Получает запись по UUID:

```php
$item = $repo->getById('550e8400-e29b-41d4-a716-446655440000');
```

✏️ updateById(string $id, string $value): bool

Обновляет значение:

```php
$repo->updateById('550e8400-e29b-41d4-a716-446655440000', 'Обновлённая коробка');
```

❌ deleteById(string $id): bool

Удаляет запись:

```php
$repo->deleteById('550e8400-e29b-41d4-a716-446655440000');
```

📋 getAll(): array

Возвращает все записи:

```php
$items = $repo->getAll();
```

***

🛑 Валидация таблиц

Если попытаться использовать недопустимое имя таблицы:

```php
new BarcodesRepository($config, 'unauthorizedTable');
```

Будет выброшено исключение:

```
InvalidArgumentException: Недопустимая таблица: unauthorizedTable
```

***

🔚 Пример использования

```php
$repo = new \ClickHouse\BarcodesRepository($config, 'typeBox');

// Добавление
$repo->create(uuid_create(UUID_TYPE_RANDOM), 'Коробка L');

// Получение
$all = $repo->getAll();

// Обновление
$repo->updateById($all[0]['id'], 'Коробка XL');

// Удаление
$repo->deleteById($all[0]['id']);
```
