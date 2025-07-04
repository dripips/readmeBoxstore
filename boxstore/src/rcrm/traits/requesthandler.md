---
icon: php
---

# RequestHandler

## 🌐 Документация по трейту RequestHandler

Добро пожаловать в документацию по трейту `RequestHandler`! 🎉 Этот PHP-трейт, расположенный в пространстве имен `RCRM\Traits`, предоставляет методы для выполнения HTTP-запросов (GET и POST) с использованием cURL. Он предназначен для упрощения взаимодействия с API CRM-системы в классах, таких как `OrderManager`, `CustomerManager` и других, обеспечивая единообразную обработку запросов. Ниже вы найдете подробное руководство по использованию, описание методов и рекомендации по внедрению.

***

### 🚀 Обзор

Трейт `RequestHandler` разработан для выполнения следующих задач:

* Выполнение POST-запросов для отправки данных на сервер (например, создание или редактирование сущностей) 📤
* Выполнение GET-запросов для получения данных с сервера (например, поиск клиентов или заказов) 📥
* Обработка ответов API в формате JSON с преобразованием в массив PHP 🧩

Трейт использует библиотеку cURL для отправки HTTP-запросов и поддерживает передачу параметров через тело запроса (POST) или строку запроса (GET). Он возвращает декодированный JSON-ответ или `null` в случае ошибки, что делает его удобным для интеграции с API CRM.

***

### 🛠️ Установка и настройка

Чтобы использовать трейт `RequestHandler`, выполните следующие шаги:

1.  **Подключите трейт**: Убедитесь, что файл трейта включен в ваш PHP-проект.

    ```php
    require_once 'path/to/RequestHandler.php';
    ```
2.  **Добавьте трейт в класс**: Используйте трейт в классах, которые будут отправлять HTTP-запросы, например, `OrderManager` или `CustomerManager`.

    ```php
    namespace RCRM;

    class OrderManager {
        use Traits\RequestHandler;

        protected string $crmDomain;
        protected string $crmKey;

        public function __construct(string $crmDomain, string $crmKey) {
            $this->crmDomain = $crmDomain;
            $this->crmKey = $crmKey;
        }

        // Пример использования
        public function createOrder(array $data): ?array {
            $url = $this->crmDomain . '/api/v5/orders/create';
            $data['apiKey'] = $this->crmKey;
            return $this->setPostRequest($url, $data);
        }
    }
    ```
3.  **Настройте окружение**: Убедитесь, что расширение cURL включено в вашей PHP-конфигурации (`extension=curl` в `php.ini`).

    ```bash
    php -i | grep cURL
    ```
4. **Используйте методы**: Вызывайте методы `setPostRequest` или `sendGetRequest` для отправки запросов к API.

***

### 📋 Методы и использование

Трейт `RequestHandler` предоставляет два публичных метода для выполнения HTTP-запросов. Ниже описаны их назначение, параметры, возвращаемые значения и рекомендации по реализации.

#### 1. `setPostRequest(string $url, array $data): ?array` 📤

Выполняет POST-запрос к указанному URL с переданными данными.

* **Параметры**:
  * `$url` (string): URL эндпоинта API (например, `https://example-crm.com/api/v5/orders/create`).
  * `$data` (array): Данные для отправки в теле запроса (ассоциативный массив).
* **Возвращает**: `?array` — декодированный JSON-ответ в виде массива или `null` в случае ошибки.
* **Логика**:
  * Преобразует `$data` в строку запроса с помощью `http_build_query`.
  * Инициализирует cURL-сессию с параметрами:
    * `CURLOPT_RETURNTRANSFER` — возвращает ответ как строку.
    * `CURLOPT_URL` — устанавливает URL.
    * `CURLOPT_POST` — указывает метод POST.
    * `CURLOPT_POSTFIELDS` — передает данные в теле запроса.
  * Выполняет запрос, декодирует JSON-ответ и закрывает cURL-сессию.
  * Возвращает `null`, если запрос не удался или ответ пустой.
*   **Пример использования**:

    ```php
    $url = 'https://example-crm.com/api/v5/orders/create';
    $data = [
        'apiKey' => 'your-api-key',
        'name' => 'Order #123',
        'customerId' => 456
    ];
    $response = $this->setPostRequest($url, $data);
    print_r($response);
    ```

***

#### 2. `sendGetRequest(string $url, array $data): ?array` 📥

Выполняет GET-запрос к указанному URL с параметрами в строке запроса.

* **Параметры**:
  * `$url` (string): URL эндпоинта API (например, `https://example-crm.com/api/v5/customers`).
  * `$data` (array): Параметры запроса (ассоциативный массив).
* **Возвращает**: `?array` — декодированный JSON-ответ в виде массива или `null` в случае ошибки.
* **Логика**:
  * Преобразует `$data` в строку запроса с помощью `http_build_query`.
  * Добавляет строку запроса к `$url` (например, `?apiKey=your-api-key&filter[name]=John`).
  * Инициализирует cURL-сессию с параметром `CURLOPT_RETURNTRANSFER` для возврата ответа.
  * Выполняет запрос, декодирует JSON-ответ и закрывает cURL-сессию.
  * Возвращает `null`, если запрос не удался или ответ пустой.
*   **Пример использования**:

    ```php
    $url = 'https://example-crm.com/api/v5/customers';
    $data = [
        'apiKey' => 'your-api-key',
        'filter' => ['name' => 'John Doe']
    ];
    $response = $this->sendGetRequest($url, $data);
    print_r($response);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Добавьте обработку ошибок cURL для предоставления информативных сообщений.

    ```php
    public function setPostRequest(string $url, array $data): ?array {
        $postData = http_build_query($data);
        $curl = curl_init();
        curl_setopt_array($curl, [
            CURLOPT_RETURNTRANSFER => true,
            CURLOPT_URL => $url,
            CURLOPT_POST => true,
            CURLOPT_POSTFIELDS => $postData,
        ]);
        $response = curl_exec($curl);
        if ($response === false) {
            $error = curl_error($curl);
            curl_close($curl);
            error_log('cURL error: ' . $error);
            return null;
        }
        curl_close($curl);
        return $response ? json_decode($response, true) : null;
    }
    ```
*   **Валидация параметров**:

    * Проверяйте, что `$url` — действительный URL с помощью `filter_var($url, FILTER_VALIDATE_URL)`.
    * Убедитесь, что `$data` — непустой массив, если это требуется API.

    ```php
    if (!filter_var($url, FILTER_VALIDATE_URL)) {
        throw new InvalidArgumentException('Invalid URL provided');
    }
    ```
* **Безопасность**:
  * Используйте HTTPS для `$url` во избежание перехвата данных.
  * Экранируйте данные в `$data` с помощью `http_build_query` для предотвращения инъекций.
  *   Добавьте проверку SSL для cURL:

      ```php
      curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, true);
      ```
* **Оптимизация**:
  *   Настройте таймауты cURL для предотвращения зависания:

      ```php
      curl_setopt($curl, CURLOPT_TIMEOUT, 30);
      curl_setopt($curl, CURLOPT_CONNECTTIMEOUT, 10);
      ```
  * Переиспользуйте cURL-дескрипторы для множественных запросов в рамках одной сессии с помощью `curl_multi_init`, если требуется высокая производительность.
*   **Логирование**: Внедрите логирование запросов и ошибок для упрощения отладки.

    ```php
    error_log('Sending POST request to ' . $url . ' with data: ' . json_encode($data));
    ```
* **Обработка JSON**:
  *   Проверяйте успешность декодирования JSON с помощью `json_last_error()`:

      ```php
      $decoded = json_decode($response, true);
      if (json_last_error() !== JSON_ERROR_NONE) {
          error_log('JSON decode error: ' . json_last_error_msg());
          return null;
      }
      return $decoded;
      ```
* **Расширяемость**:
  * Рассмотрите добавление поддержки других методов HTTP (PUT, DELETE) в трейт.
  * Позвольте передавать дополнительные заголовки или параметры cURL через аргументы метода.

***

### 🐛 Устранение неполадок

* **Ошибка cURL**:
  * Проверьте, включено ли расширение cURL в PHP (`php -i | grep cURL`).
  *   Добавьте обработку ошибок cURL:

      ```php
      if ($response === false) {
          $error = curl_error($curl);
          curl_close($curl);
          throw new RuntimeException('cURL error: ' . $error);
      }
      ```
  * Убедитесь, что `$url` доступен и сервер CRM отвечает.
* **Пустой ответ (`null`)**:
  * Проверьте, возвращает ли сервер ответ (`curl_getinfo($curl, CURLINFO_HTTP_CODE)`).
  * Убедитесь, что API возвращает корректный JSON.
  * Проверьте сетевые настройки (например, файрволл или DNS).
* **Некорректный JSON**:
  * Проверьте формат ответа сервера с помощью `curl_getinfo($curl, CURLINFO_CONTENT_TYPE)` и убедитесь, что это `application/json`.
  *   Логируйте сырой ответ для отладки:

      ```php
      error_log('Raw response: ' . $response);
      ```
* **Параметры не передаются**:
  * Убедитесь, что `$data` корректно преобразуется в строку запроса (`http_build_query`).
  * Проверьте, что параметры в `$data` соответствуют ожиданиям API (например, `apiKey`, `filter`).
* **Таймауты или медленные запросы**:
  * Настройте параметры `CURLOPT_TIMEOUT` и `CURLOPT_CONNECTTIMEOUT`.
  * Проверьте производительность сервера CRM.

***
