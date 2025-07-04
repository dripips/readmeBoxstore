---
icon: php
---

# CostManager

## 📊 Документация по классу CostManager

Добро пожаловать в документацию по классу `CostManager`! 🎉 Этот PHP-класс, расположенный в пространстве имен `RCRM`, предназначен для управления расходами в CRM-системе через API. Он предоставляет методы для получения, создания, удаления и пакетного добавления расходов, а также для управления основными расходами, связанными с заказами. Класс использует объект `Request` для отправки HTTP-запросов и интегрируется с `OrderManager` для работы с данными заказов. Ниже вы найдете подробное руководство по использованию, описание методов и рекомендации по внедрению.

***

### 🚀 Обзор

Класс `CostManager` разработан для выполнения следующих задач:

* Получение списка расходов по фильтрам или для конкретного заказа 📋
* Создание, удаление и пакетное добавление расходов через API CRM 💸
* Управление основными расходами заказа (материалы, работа, электричество, амортизация) 🛠️
* Интеграция с `OrderManager` для получения данных о заказах и расчета стоимости расходов 📦

Класс хранит массив кодов основных расходов (`mainCostsCodes`) и использует объект `Request` для взаимодействия с API CRM. Он поддерживает автоматизацию добавления расходов к заказам, минимизируя дублирование, и рассчитывает стоимость на основе свойств элементов заказа.

***

### 🛠️ Установка и настройка

Чтобы использовать класс `CostManager`, выполните следующие шаги:

1.  **Подключите класс и зависимости**: Убедитесь, что файлы класса `CostManager` и класса `Request` включены в ваш PHP-проект.

    ```php
    require_once 'path/to/CostManager.php';
    require_once 'path/to/Request.php';
    ```
2.  **Создайте класс Request**: Убедитесь, что класс `Request` существует и предоставляет метод `send` для отправки HTTP-запросов. Пример:

    ```php
    namespace Request;

    class Request {
        private $domain;
        private $headers;

        public function __construct(string $domain, array $headers) {
            $this->domain = $domain;
            $this->headers = $headers;
        }

        public function send(string $method, string $endpoint, array $query = [], array $body = []): array {
            // Реализация HTTP-запроса (например, с помощью cURL)
        }
    }
    ```
3.  **Создайте экземпляр класса**: Передайте домен CRM и API-ключ в конструктор.

    ```php
    use RCRM\CostManager;

    $crmDomain = 'https://example-crm.com';
    $crmKey = 'your-api-key';
    $costManager = new CostManager($crmDomain, $crmKey);
    ```
4.  **Интеграция с OrderManager**: Для методов, использующих данные заказов, создайте экземпляр `OrderManager`.

    ```php
    use RCRM\OrderManager;

    $orderManager = new OrderManager($crmDomain, $crmKey);
    ```
5. **Используйте методы**: Вызывайте методы для управления расходами или добавления их к заказам.

***

### 📋 Методы и использование

Класс `CostManager` предоставляет публичные и приватные методы для работы с расходами. Ниже описаны все методы.

#### 1. `__construct(string $crmDomain, string $crmKey)` 🛠️

Инициализирует класс, сохраняя API-ключ и создавая объект `Request`.

* **Параметры**:
  * `$crmDomain` (string): Домен CRM-системы (например, `https://example-crm.com`).
  * `$crmKey` (string): API-ключ для аутентификации.
* **Возвращает**: Ничего
* **Логика**:
  * Сохраняет `$crmKey` в свойстве `apiKey`.
  * Создает объект `Request` с доменом и заголовком `Content-Type: application/json`.
*   **Пример**:

    ```php
    $costManager = new CostManager('https://example-crm.com', 'your-api-key');
    ```

***

#### 2. `getMainCostsCodes(): array` 📋

Возвращает массив кодов основных расходов.

* **Параметры**: Нет
* **Возвращает**: Массив с кодами расходов (`maker-work-cost`, `material-cost`, `electric-cost`, `amortization-cost`) и их соответствием свойствам (`costWork`, `costMaterial`, `costElectricity`, `costAmortization`).
* **Логика**:
  * Возвращает защищенное свойство `$mainCostsCodes`.
*   **Пример**:

    ```php
    $codes = $costManager->getMainCostsCodes();
    print_r($codes);
    // Вывод: ['maker-work-cost' => 'costWork', 'material-cost' => 'costMaterial', ...]
    ```

***

#### 3. `getCosts($body): array` 📋

Получает список расходов по фильтру через API CRM.

* **Параметры**:
  * `$body` (array): Параметры фильтрации (зависят от API CRM).
* **Возвращает**: Массив — ответ от API.
* **Логика**:
  * Добавляет `apiKey` в `$body`.
  * Формирует строку запроса с помощью `http_build_query`.
  * Отправляет GET-запрос на `/api/v5/costs`.
*   **Пример**:

    ```php
    $body = [
        'filter' => [
            'status' => 'active'
        ]
    ];
    $response = $costManager->getCosts($body);
    print_r($response);
    ```

***

#### 4. `createCost($body): array` 💸

Создает новый расход в CRM.

* **Параметры**:
  * `$body` (array): Данные расхода (поля зависят от API CRM).
* **Возвращает**: Массив — ответ от API.
* **Логика**:
  * Добавляет `apiKey` в `$body`.
  * Отправляет POST-запрос на `/api/v5/costs/create`.
*   **Пример**:

    ```php
    $body = [
        'dateFrom' => '2025-05-16',
        'summ' => 100.50,
        'costItem' => 'material-cost',
        'order' => ['id' => '123']
    ];
    $response = $costManager->createCost($body);
    print_r($response);
    ```

***

#### 5. `deleteCost($body): array` 💸

Удаляет расход в CRM.

* **Параметры**:
  * `$body` (array): Данные для удаления расхода.
* **Возвращает**: Массив — ответ от API.
* **Логика**:
  * Добавляет `apiKey` в `$body`.
  * Отправляет POST-запрос на `/api/v5/costs/create` (возможно, ошибка в эндпоинте, см. примечание).
* **Примечание**:
  * Эндпоинт `/api/v5/costs/create` выглядит некорректным для удаления. Вероятно, должен быть `/api/v5/costs/delete` или аналогичный.
  * Проверьте документацию API CRM для правильного эндпоинта.
*   **Пример**:

    ```php
    $body = [
        'id' => '456'
    ];
    $response = $costManager->deleteCost($body);
    print_r($response);
    ```

***

#### 6. `getOrderCosts($number): array` 📋

Получает все расходы, связанные с конкретным заказом.

* **Параметры**:
  * `$number` (string): Номер заказа (очищается от букв).
* **Возвращает**: Массив — ответ от API с расходами заказа.
* **Логика**:
  * Очищает `$number` от нечисловых символов с помощью `preg_replace('/[^0-9]/', '', $number)`.
  * Формирует фильтр `filter.orderIds` с номером заказа.
  * Добавляет `apiKey` и отправляет GET-запрос на `/api/v5/costs`.
*   **Пример**:

    ```php
    $response = $costManager->getOrderCosts('ORD123');
    print_r($response);
    ```

***

#### 7. `uploadCosts($body): array` 💸

Пакетно создает расходы в CRM.

* **Параметры**:
  * `$body` (array): Данные расходов (поле `costs` должно содержать JSON-строку).
* **Возвращает**: Массив — ответ от API.
* **Логика**:
  * Добавляет `apiKey` в `$body`.
  * Отправляет POST-запрос на `/api/v5/costs/upload`.
*   **Пример**:

    ```php
    $body = [
        'costs' => json_encode([
            [
                'dateFrom' => '2025-05-16',
                'summ' => 100.50,
                'costItem' => 'material-cost',
                'order' => ['id' => '123']
            ]
        ])
    ];
    $response = $costManager->uploadCosts($body);
    print_r($response);
    ```

***

#### 8. `addMainCosts($number, $orderManager): array` 💸

Добавляет основные расходы к заказу, если они еще не существуют.

* **Параметры**:
  * `$number` (string): Номер заказа (очищается от букв).
  * `$orderManager` (object): Экземпляр класса `OrderManager` для получения данных заказа.
* **Возвращает**: Массив — результат пакетного создания расходов или сообщение об ошибке.
* **Логика**:
  * Очищает `$number` от нечисловых символов.
  * Получает текущие расходы заказа через `getOrderCosts`.
  * Исключает уже существующие основные расходы из `$mainCostsCodes`.
  * Если все основные расходы уже есть, возвращает ошибку с сообщением.
  * Для каждого недостающего расхода рассчитывает стоимость через `calculateCostPrice`.
  * Формирует массив данных для создания расходов (дата, сумма, код, ID заказа).
  * Отправляет пакетный запрос через `uploadCosts`.
*   **Пример**:

    ```php
    $response = $costManager->addMainCosts('ORD123', $orderManager);
    print_r($response);
    ```

***

#### 9. `calculateCostPrice($number, $code, $orderManager)` (приватный) 📊

Рассчитывает стоимость расхода для заказа.

* **Параметры**:
  * `$number` (string): Номер заказа.
  * `$code` (string): Код расхода (например, `material-cost`).
  * `$orderManager` (object): Экземпляр `OrderManager`.
* **Возвращает**: `float|int` — стоимость расхода.
* **Логика**:
  * Получает информацию о заказе через `$orderManager->getOrderInfo`.
  * Итерируется по элементам заказа (`items`), извлекая свойства (`properties`).
  * Находит свойство, соответствующее `$mainCostsCodes[$code]` (например, `costMaterial` для `material-cost`).
  * Рассчитывает стоимость как `value * quantity` для каждого элемента.
  * Суммирует стоимости по всем элементам.
* **Примечание**: Вызывается внутри `addMainCosts`.
*   **Пример**:

    ```php
    // Внутренний вызов
    $cost = $this->calculateCostPrice('123', 'material-cost', $orderManager);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Реализуйте надежную обработку ошибок в `Request::send` и проверяйте ответы API.

    ```php
    $response = $this->request->send('GET', $endpoint);
    if (!$response['success']) {
        error_log('CRM API error: ' . $response['message']);
        return ['success' => false, 'message' => $response['message']];
    }
    ```
* **Валидация параметров**:
  * Проверяйте, что `$crmDomain` — действительный URL, а `$crmKey` — непустая строка.
  * Убедитесь, что `$number` содержит цифры после очистки.
  * Валидируйте `$body` на наличие обязательных полей (например, `summ`, `costItem`, `order.id`).
  * Проверяйте, что `$orderManager` — экземпляр `OrderManager` с корректной конфигурацией.
* **Безопасность**:
  * Храните `$crmKey` в безопасном месте (например, в переменных окружения).
  * Используйте `http_build_query` и JSON-кодирование для безопасной передачи данных.
* **Оптимизация**:
  * Кэшируйте результаты `getOrderCosts`, если данные о расходах редко меняются.
  * Объединяйте запросы к API, если это поддерживается CRM, чтобы снизить нагрузку.
*   **Логирование**: Внедрите логирование запросов и ошибок для упрощения отладки.

    ```php
    error_log('Creating cost for order ' . $number . ': ' . json_encode($body));
    ```
* **Интеграция с OrderManager**:
  * Убедитесь, что `$orderManager->getOrderInfo` возвращает ожидаемые данные (`items`, `properties`).
  * Проверяйте наличие свойств (`costWork`, `costMaterial`, и т.д.) в элементах заказа.
* **Корректность эндпоинтов**:
  * Проверьте эндпоинт в `deleteCost` (`/api/v5/costs/create` выглядит ошибочным, должен быть `/api/v5/costs/delete` или аналогичный).
  * Сверьтесь с документацией API CRM для всех эндпоинтов.

***

### 🐛 Устранение неполадок

* **Ошибка API CRM**:
  * Проверьте, что `$crmDomain` и `$crmKey` корректны и API доступен.
  * Убедитесь, что `Request::send` правильно обрабатывает HTTP-запросы и возвращает ответы.
  * Проверьте формат `$body` в методах `createCost`, `uploadCosts`, `deleteCost`.
* **Некорректный номер заказа**:
  * Убедитесь, что `$number` содержит цифры после очистки в `getOrderCosts` и `addMainCosts`.
  * Проверьте, существует ли заказ с указанным номером в CRM.
* **Отсутствие данных о расходах**:
  * Проверьте, возвращает ли `getOrderCosts` ожидаемые данные (`costs`).
  * Убедитесь, что `$mainCostsCodes` содержит актуальные коды расходов.
* **Ошибки расчета стоимости**:
  * Проверьте, что `$orderManager->getOrderInfo` возвращает корректные данные (`items`, `properties`).
  * Убедитесь, что свойства (`costWork`, `costMaterial`, и т.д.) присутствуют в элементах заказа.
  * Проверьте, что `$mainCostsCodes[$code]` соответствует свойству в заказе.
* **Проблемы с пакетным созданием**:
  * Убедитесь, что поле `costs` в `$body` для `uploadCosts` — корректная JSON-строка.
  * Проверьте, что данные расходов (`dateFrom`, `summ`, `costItem`, `order.id`) заполнены правильно.

***
