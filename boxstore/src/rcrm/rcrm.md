---
icon: php
---

# RCRM

## 🌐 Документация по классу RCRM

Добро пожаловать в документацию по классу `RCRM`! 🎉 Этот PHP-класс, расположенный в пространстве имен `RCRM`, служит основным интерфейсом для взаимодействия с CRM-системой через API. Он предоставляет доступ к менеджерам заказов (`OrderManager`) и клиентов (`CustomerManager`), обеспечивая централизованную точку входа для управления заказами и клиентами. Класс использует объект `Request` для отправки HTTP-запросов и поддерживает конфигурацию домена CRM и API-ключа. Ниже вы найдете подробное руководство по использованию, описание методов и рекомендации по внедрению.

***

### 🚀 Обзор

Класс `RCRM` разработан для выполнения следующих задач:

* Инициализация подключения к CRM-системе с использованием домена и API-ключа 🔑
* Создание экземпляров менеджеров для работы с заказами (`OrderManager`) и клиентами (`CustomerManager`) 📦👤
* Обеспечение единообразного доступа к API CRM через объект `Request` 📡

Класс действует как фабрика для создания специализированных менеджеров, передавая им конфигурацию CRM (`crmDomain` и `crmKey`). Это упрощает интеграцию с различными компонентами системы и обеспечивает согласованность в работе с API.

***

### 🛠️ Установка и настройка

Чтобы использовать класс `RCRM`, выполните следующие шаги:

1.  **Подключите класс и зависимости**: Убедитесь, что файлы класса `RCRM`, а также классы `Request`, `OrderManager` и `CustomerManager` включены в ваш PHP-проект.

    ```php
    require_once 'path/to/RCRM.php';
    require_once 'path/to/Request.php';
    require_once 'path/to/OrderManager.php';
    require_once 'path/to/CustomerManager.php';
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
    use RCRM\RCRM;

    $crmDomain = 'https://example-crm.com';
    $crmKey = 'your-api-key';
    $rcrm = new RCRM($crmDomain, $crmKey);
    ```
4.  **Используйте менеджеры**: Получите доступ к `OrderManager` или `CustomerManager` через соответствующие методы.

    ```php
    $orderManager = $rcrm->orders();
    $customerManager = $rcrm->customers();
    ```
5. **Используйте методы менеджеров**: Вызывайте методы `OrderManager` или `CustomerManager` для работы с заказами и клиентами.

***

### 📋 Методы и использование

Класс `RCRM` предоставляет три метода: конструктор и два метода для создания менеджеров. Ниже описаны все методы.

#### 1. `__construct(string $crmDomain, string $crmKey)` 🛠️

Инициализирует класс, сохраняя домен CRM, API-ключ и создавая объект `Request`.

* **Параметры**:
  * `$crmDomain` (string): Домен CRM-системы (например, `https://example-crm.com`).
  * `$crmKey` (string): API-ключ для аутентификации.
* **Возвращает**: Ничего
* **Логика**:
  * Сохраняет `$crmDomain` и `$crmKey` в защищенных свойствах.
  * Создает объект `Request` с доменом и заголовком `Content-Type: application/json`.
*   **Пример**:

    ```php
    $rcrm = new RCRM('https://example-crm.com', 'your-api-key');
    ```

***

#### 2. `orders(): OrderManager` 📦

Создает и возвращает экземпляр `OrderManager`.

* **Параметры**: Нет
* **Возвращает**: Экземпляр `OrderManager`, настроенный с текущими `$crmDomain` и `$crmKey`.
* **Логика**:
  * Создает новый объект `OrderManager`, передавая в конструктор сохраненные `$crmDomain` и `$crmKey`.
*   **Пример**:

    ```php
    $orderManager = $rcrm->orders();
    $response = $orderManager->createOrder(['name' => 'Order #123']);
    print_r($response);
    ```

***

#### 3. `customers(): CustomerManager` 👤

Создает и возвращает экземпляр `CustomerManager`.

* **Параметры**: Нет
* **Возвращает**: Экземпляр `CustomerManager`, настроенный с текущими `$crmDomain` и `$crmKey`.
* **Логика**:
  * Создает новый объект `CustomerManager`, передавая в конструктор сохраненные `$crmDomain` и `$crmKey`.
*   **Пример**:

    ```php
    $customerManager = $rcrm->customers();
    $response = $customerManager->searchCustomer(['name' => 'John Doe']);
    print_r($response);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Реализуйте надежную обработку ошибок в `Request::send` и проверяйте ответы API в менеджерах (`OrderManager`, `CustomerManager`).

    ```php
    $response = $orderManager->createOrder($data);
    if (!$response['success']) {
        error_log('CRM API error: ' . $response['message']);
    }
    ```
*   **Валидация параметров**:

    * Проверяйте, что `$crmDomain` — действительный URL (например, начинается с `http://` или `https://`).
    * Убедитесь, что `$crmKey` — непустая строка.

    ```php
    if (empty($crmKey) || !filter_var($crmDomain, FILTER_VALIDATE_URL)) {
        throw new InvalidArgumentException('Invalid CRM domain or API key');
    }
    ```
* **Безопасность**:
  * Храните `$crmKey` в безопасном месте (например, в переменных окружения).
  * Используйте HTTPS для `$crmDomain` во избежание перехвата данных.
*   **Оптимизация**:

    * Кэшируйте экземпляры `OrderManager` и `CustomerManager`, если они часто используются в одном запросе.

    ```php
    $orderManager = $rcrm->orders(); // Кэшировать для повторного использования
    ```

    * Минимизируйте количество создания объектов `Request`, если это возможно.
*   **Логирование**: Внедрите логирование создания экземпляров и ошибок для упрощения отладки.

    ```php
    error_log('Initialized RCRM with domain: ' . $crmDomain);
    ```
* **Расширяемость**:
  * Рассмотрите добавление методов для создания других менеджеров (например, `products()`, `invoices()`), если CRM поддерживает дополнительные сущности.
  * Используйте интерфейсы для `OrderManager` и `CustomerManager`, чтобы обеспечить гибкость при замене реализаций.

***

### 🐛 Устранение неполадок

* **Ошибка создания объекта `Request`**:
  * Проверьте, что класс `Request` существует и корректно реализован.
  * Убедитесь, что переданные заголовки (`Content-Type: application/json`) поддерживаются API.
* **Ошибка API CRM**:
  * Проверьте, что `$crmDomain` указывает на действующий сервер CRM и API доступен.
  * Убедитесь, что `$crmKey` действителен и имеет права на выполнение операций.
* **Проблемы с менеджерами**:
  * Проверьте, что `OrderManager` и `CustomerManager` корректно используют трейт `RequestHandler` (или `Request` в зависимости от реализации).
  * Убедитесь, что методы менеджеров возвращают ожидаемые данные и обрабатывают ошибки API.
* **Некорректный ответ API**:
  * Проверьте документацию API CRM на предмет правильных эндпоинтов и структуры ответов.
  * Убедитесь, что `Request::send` корректно обрабатывает JSON-ответы.

***
