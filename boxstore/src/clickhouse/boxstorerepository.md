---
description: >-
  Упрощает доступ к данным коробок в базе ClickHouse через объектную обёртку.
  Реализует базовый CRUD и поиск с фильтрами и пагинацией.
icon: php
---

# BoxStoreRepository

Класс `BoxStoreRepository` предоставляет удобный интерфейс для работы с таблицей `boxStore` в ClickHouse. Включает операции вставки, обновления, удаления, поиска и полнотекстового поиска по имени.

### 📦 Назначение

Упрощает доступ к данным коробок в базе ClickHouse через объектную обёртку. Реализует базовый CRUD и поиск с фильтрами и пагинацией.

***

### 🔧 Конфигурация

#### Конструктор

```php
public function __construct(array $config)
```

**Параметры:**

| Название  | Тип   | Описание                        |
| --------- | ----- | ------------------------------- |
| `$config` | array | Конфиг подключения к ClickHouse |

**Пример:**

```php
$repo = new BoxStoreRepository([
    'host' => 'localhost',
    'port' => 8123,
    'users' => [
        'write' => [
            'username' => 'default',
            'password' => '',
        ]
    ]
]);
```

***

### 📥 Вставка

#### insert

```php
public function insert(array $data): bool
```

Добавляет новую запись в таблицу. Если UUID не передан, будет сгенерирован автоматически.

**Пример:**

```php
$repo->insert([
    'name' => 'Коробка А',
    'typeBox' => 'T1',
    'colorBox' => 'Белый',
    'lBox' => 300,
    'wBox' => 200,
    'hBox' => 150,
    'amount' => 100,
    'barcode' => '123456789',
    'article' => 'ART001',
    'xBox' => '10',
    'yBox' => '20',
    'xBoxForBox' => '30',
    'yBoxForBox' => '40'
]);
```

***

### 🔍 Получение

#### getByUuid

```php
public function getByUuid(string $uuid): ?array
```

Получает запись по UUID.

***

### ✏️ Обновление

#### updateByUuid

```php
public function updateByUuid(string $uuid, array $data): bool
```

Обновляет поля записи по UUID.

**Пример:**

```php
$repo->updateByUuid($uuid, [
    'name' => 'Новое имя',
    'colorBox' => 'Красный',
]);
```

***

### ❌ Удаление

#### deleteByUuid

```php
public function deleteByUuid(string $uuid): bool
```

Удаляет запись по UUID.

***

### 🔎 Поиск

#### search

```php
public function search(array $filters = []): array
```

Возвращает список записей, соответствующих фильтрам (строгое сравнение `=` по нескольким полям).

**Пример:**

```php
$repo->search([
    'typeBox' => 'T1',
    'colorBox' => 'Белый'
]);
```

***

### 📄 Поиск с пагинацией

#### searchWithPagination

```php
public function searchWithPagination(array $filters = [], int $page = 1, int $limit = 20): array
```

Возвращает записи с поддержкой пагинации и фильтрации.

**Пример:**

```php
$repo->searchWithPagination(['typeBox' => 'T2'], 2, 10);
```

***

### ⚡ Быстрый поиск

#### fastSearch

```php
public function fastSearch(string $term, int $limit = 10): array
```

Полнотекстовый поиск по полю `name`. Использует `lower(name) LIKE '%term%'` для эмуляции `ILIKE`.

**Пример:**

```php
$repo->fastSearch('короб');
```

***

### 🆔 Генерация UUID

#### generateUuid (private)

```php
private function generateUuid(): string
```

Генерирует UUID v4 без внешних зависимостей. Используется при вставке, если не передан `uuid`.

***

### 🗃 Структура таблицы `boxStore`

```sql
CREATE TABLE boxStore (
    uuid UUID,
    name String,
    typeBox String,
    colorBox String,
    lBox UInt16,
    wBox UInt16,
    hBox UInt16,
    amount UInt16,
    barcode String,
    article String,
    xBox String,
    yBox String,
    xBoxForBox String,
    yBoxForBox String
) ENGINE = MergeTree()
ORDER BY (name);
```
