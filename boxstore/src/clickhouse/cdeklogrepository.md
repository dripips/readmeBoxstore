---
description: CdekLogRepository — работа с логами доставки и расчётами
icon: php
---

# CdekLogRepository

## 📦 CdekLogRepository — работа с логами доставки и расчётами

Файл: src/ClickHouse/CdekLogRepository.php\
Назначение: управление логами доставки CDEK и результатами расчётов в ClickHouse

***

### 📌 Что делает этот класс?

* 🚛 Логирует информацию по доставке CDEK (таблица cdek\_log)
* ✏️ Позволяет создать, получить, изменить или удалить запись по заказу
* 🧮 Записывает и извлекает логи расчетов (таблица logsCalculated)

***

### ⚙️ Подключение

Подключается к ClickHouse через массив конфигурации:

```php
$config = [
    'clickhouse' => [
        'logs' => [
            'host' => 'localhost',
            'port' => 8123,
            'users' => [
                'write' => [
                    'username' => 'your_user',
                    'password' => 'your_pass'
                ]
            ]
        ]
    ]
];

$repo = new \ClickHouse\CdekLogRepository($config);
```

***

### 🔧 Методы для работы с cdek\_log

🆕 create(string $orderNumber, string $boxstorePrice, string $cdekPrice): bool

Добавляет новую запись:

```php
$repo->create('ORDER123', '1200', '1150');
```

🔎 getByOrderNumber(string $orderNumber): ?array

Получает запись по номеру заказа:

```php
$order = $repo->getByOrderNumber('ORDER123');
```

📝 updateByOrderNumber(string $orderNumber, array $data): bool

Обновляет цены по заказу:

```php
$repo->updateByOrderNumber('ORDER123', [
    'boxstore_price' => '1250',
    'cdek_price' => '1190'
]);
```

❌ deleteByOrderNumber(string $orderNumber): bool

Удаляет запись:

```php
$repo->deleteByOrderNumber('ORDER123');
```

📅 getByPeriod(string $startDate, string $endDate): array

Получает список записей за период:

```php
$repo->getByPeriod('2025-05-01 00:00:00', '2025-05-15 23:59:59');
```

***

### 🧮 Методы для logsCalculated

🆕 addCalculatedLog(string $calculated): bool

Добавляет строку в лог расчетов:

```php
$repo->addCalculatedLog('{"weight": 3.4, "price": 800}');
```

📋 getAllCalculatedLogs(): array

Получить все записи:

```php
$logs = $repo->getAllCalculatedLogs();
```

📆 getCalculatedLogsByDate(string $date): array

Получить записи по дате:

```php
$logs = $repo->getCalculatedLogsByDate('2025-05-15');
```

***

🧪 Пример использования

```php
$config = require 'config.php';
$repo = new \ClickHouse\CdekLogRepository($config);

// Добавляем лог
$repo->create('ORDER-999', '1345', '1290');

// Получаем лог
$log = $repo->getByOrderNumber('ORDER-999');

// Обновляем
$repo->updateByOrderNumber('ORDER-999', [
    'boxstore_price' => '1375',
    'cdek_price' => '1310'
]);

// Удаляем
$repo->deleteByOrderNumber('ORDER-999');
```
