---
description: CalculationLogRepository — логирование в ClickHouse
icon: php
---

# CalculationLogRepository

## 📊 CalculationLogRepository — логирование в ClickHouse

Файл: src/ClickHouse/CalculationLogRepository.php\
Назначение: логирование результатов расчётов в таблицу ClickHouse: logsCalculated

***

### 🧠 Назначение

Класс CalculationLogRepository выполняет следующие функции:

* ✍️ Добавляет результат расчёта в ClickHouse;
* 📖 Извлекает все логи;
* 📅 Извлекает логи по дате.

***

### ⚙️ Конфигурация подключения

Для подключения используется массив $configAll:

```php
$configAll = [
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
```

***

### 🔧 Конструктор

```php
public function __construct(array $configAll)
```

Подключается к базе ClickHouse, используя параметры:

* host
* port
* username
* password

***

### ✅ Методы класса

#### addCalculatedLog(string $calculated): bool

Добавляет строку (обычно JSON) в таблицу logsCalculated.

```php
$log->addCalculatedLog('{"weight": 2.5, "price": 350}');
```

* calculated — произвольное строковое значение.
* В поле date автоматически добавляется текущее время (Europe/Moscow).

Возвращает true при успешной записи.

***

#### getAllCalculatedLogs(): array

Получает все строки из таблицы logsCalculated.

```php
$logs = $log->getAllCalculatedLogs();

foreach ($logs as $row) {
    echo $row['calculated'] . "\n";
}
```

***

#### getCalculatedLogsByDate(string $date): array

Фильтрует записи по дате.

```php
$logs = $log->getCalculatedLogsByDate('2025-05-15');

foreach ($logs as $row) {
    echo $row['calculated'] . "\n";
}
```

Формат даты: YYYY-MM-DD

***

### 🧪 Пример использования

```php
use ClickHouse\CalculationLogRepository;

$config = require 'config.php';
$log = new CalculationLogRepository($config);

// Добавляем лог
$log->addCalculatedLog(json_encode([
    'article' => 'A12345',
    'price' => 250
]));

// Получаем логи за сегодня
$today = date('Y-m-d');
$logs = $log->getCalculatedLogsByDate($today);
```

***

📌 Полезно для отладки, аналитики, сбора статистики или логирования событий, связанных с расчётами.
