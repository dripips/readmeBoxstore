---
icon: php
---

# CustomerManager

## 👤 Документация по классу CustomerManager

Добро пожаловать в документацию по классу `CustomerManager`! 🎉 Этот PHP-класс, расположенный в пространстве имен `RCRM`, предназначен для управления клиентами в CRM-системе через API. Он предоставляет методы для поиска, редактирования и получения информации о клиентах, а также для получения списка заказов клиента. Класс использует трейт `RequestHandler` для отправки HTTP-запросов и поддерживает работу с фильтрами, пагинацией и поиском по телефону или email. Ниже вы найдете подробное руководство по использованию, описание методов и рекомендации по внедрению.

***

### 🚀 Обзор

Класс `CustomerManager` разработан для выполнения следующих задач:

* Поиск клиентов по различным критериям (телефон, email, произвольные фильтры) 🔍
* Редактирование информации о клиентах 📝
* Получение информации о клиенте по ID 👤
* Получение списка заказов клиента с пагинацией 📋
* Тестирование функциональности через метод `test` ✅

Класс использует трейт `RequestHandler` для отправки GET- и POST-запросов к API CRM, поддерживает пагинацию для обработки больших объемов данных и предоставляет удобный интерфейс для работы с клиентами в системе.

***

### 🛠️ Установка и настройка

Чтобы использовать класс `CustomerManager`, выполните следующие шаги:

1.  **Подключите класс и трейт**: Убедитесь, что файлы класса `CustomerManager` и трейта `RequestHandler` включены в ваш PHP-проект.

    ```php
    require_once 'path/to/CustomerManager.php';
    require_once 'path/to/RequestHandler.php';
    ```
2.  **Создайте трейт RequestHandler**: Убедитесь, что трейт `RequestHandler` существует и содержит методы `setPostRequest` и `sendGetRequest` для отправки HTTP-запросов. Пример:

    ```php
    namespace RCRM\Traits;

    trait RequestHandler {
        protected function setPostRequest(string $url, array $data): ?array {
            // Реализация POST-запроса (например, с помощью cURL)
        }

        protected function sendGetRequest(string $url, array $data): ?array {
            // Реализация GET-запроса (например, с помощью cURL)
        }
    }
    ```
3.  **Создайте экземпляр класса**: Передайте домен CRM и API-ключ в конструктор.

    ```php
    use RCRM\CustomerManager;

    $crmDomain = 'https://example-crm.com';
    $crmKey = 'your-api-key';
    $customerManager = new CustomerManager($crmDomain, $crmKey);
    ```
4. **Используйте методы**: Вызывайте методы для поиска клиентов, редактирования данных или получения заказов.

***

### 📋 Методы и использование

Класс `CustomerManager` предоставляет семь публичных методов для работы с клиентами и их заказами. Ниже описаны все методы.

#### 1. `__construct(string $crmDomain, string $crmKey)` 🛠️

Инициализирует класс, сохраняя домен CRM и API-ключ.

* **Параметры**:
  * `$crmDomain` (string): Домен CRM-системы (например, `https://example-crm.com`).
  * `$crmKey` (string): API-ключ для аутентификации.
* **Возвращает**: Ничего
*   **Пример**:

    ```php
    $customerManager = new CustomerManager('https://example-crm.com', 'your-api-key');
    ```

***

#### 2. `test(): ?array` ✅

Тестовый метод для проверки работоспособности класса.

* **Параметры**: Нет
* **Возвращает**: Массив с ключами `result` (1) и `data` ("ok").
* **Логика**:
  * Возвращает фиксированный результат для тестирования.
*   **Пример**:

    ```php
    $response = $customerManager->test();
    print_r($response);
    // Вывод: ['result' => 1, 'data' => 'ok']
    ```

***

#### 3. `editCustomer(int $customerId, array $data)` 📝

Редактирует информацию о клиенте по его ID.

* **Параметры**:
  * `$customerId` (int): ID клиента в CRM.
  * `$data` (array): Данные для обновления клиента (зависят от API CRM).
* **Возвращает**: Массив с ключами:
  * `result` (bool): Всегда `true` (при успешном запросе).
  * `data` (array): Ответ от API.
* **Логика**:
  * Добавляет `apiKey` в `$data`.
  * Отправляет POST-запрос на `/api/v5/customers/$customerId/edit` с помощью `setPostRequest`.
*   **Пример**:

    ```php
    $data = [
        'firstName' => 'John',
        'lastName' => 'Doe',
        'email' => 'john.doe@example.com'
    ];
    $response = $customerManager->editCustomer(123, $data);
    print_r($response);
    ```

***

#### 4. `searchCustomer(array $data): ?array` 🔍

Ищет одного клиента по заданным критериям.

* **Параметры**:
  * `$data` (array): Фильтр поиска (например, `name`, `email`).
* **Возвращает**: Массив с ключами:
  * `result` (bool): Успешность поиска (`true`/`false`).
  * `data` (array): Данные последнего найденного клиента (при успехе).
  * `text_error` (string): Сообщение об ошибке (при неудаче).
* **Логика**:
  * Формирует фильтр с `apiKey` и переданным `$data`.
  * Отправляет GET-запрос на `/api/v5/customers`.
  * Если есть результаты (`totalPageCount > 0`), возвращает последнего клиента из списка.
  * Иначе возвращает ошибку "Клиент не найден".
*   **Пример**:

    ```php
    $data = ['name' => 'John Doe'];
    $response = $customerManager->searchCustomer($data);
    print_r($response);
    ```

***

#### 5. `searchCustomers(array $data): ?array` 🔍

Ищет всех клиентов по заданным критериям с пагинацией.

* **Параметры**:
  * `$data` (array): Фильтр поиска.
* **Возвращает**: Массив с ключами:
  * `result` (bool): Успешность поиска (`true`/`false`).
  * `data` (array): Список ID найденных клиентов (при успехе).
  * `text_error` (string): Сообщение об ошибке (при неудаче).
* **Логика**:
  * Итерируется по страницам результатов (до `totalPageCount`), используя `limit=100`.
  * Собирает ID клиентов в массив `$result`.
  * Если найдены клиенты, возвращает их ID.
  * Иначе возвращает ошибку "Клиент не найден".
*   **Пример**:

    ```php
    $data = ['name' => 'John'];
    $response = $customerManager->searchCustomers($data);
    print_r($response);
    // Вывод: ['result' => true, 'data' => [123, 456, ...]]
    ```

***

#### 6. `searchCustomersPhoneEmail(array $data): ?array` 🔍

Ищет клиентов по телефону или email.

* **Параметры**:
  * `$data` (array): Данные с ключами `phone` и/или `email`.
* **Возвращает**: Массив с ключами:
  * `result` (bool): Успешность поиска (`true`/`false`).
  * `data` (array): Список найденных клиентов (при успехе).
  * `text_error` (string): Сообщение об ошибке (при неудаче).
* **Логика**:
  * Если указан `phone`, ищет по `name` с значением телефона.
  * Если указан `email`, ищет по `email`.
  * Если найдены клиенты (`totalPageCount > 0`), возвращает весь список клиентов.
  * Иначе возвращает ошибку "Клиент не найден".
*   **Пример**:

    ```php
    $data = ['phone' => '+79991234567', 'email' => 'john.doe@example.com'];
    $response = $customerManager->searchCustomersPhoneEmail($data);
    print_r($response);
    ```

***

#### 7. `searchCustomerPhoneEmail(array $data): ?array` 🔍

Ищет одного клиента по телефону или email.

* **Параметры**:
  * `$data` (array): Данные с ключами `phone` и/или `email`.
* **Возвращает**: Массив с ключами:
  * `result` (bool): Успешность поиска (`true`/`false`).
  * `data` (array): Данные последнего найденного клиента (при успехе).
  * `text_error` (string): Сообщение об ошибке (при неудаче).
* **Логика**:
  * Аналогично `searchCustomersPhoneEmail`, но возвращает только последнего клиента из списка.
*   **Пример**:

    ```php
    $data = ['phone' => '+79991234567'];
    $response = $customerManager->searchCustomerPhoneEmail($data);
    print_r($response);
    ```

***

#### 8. `getCustomerOrders(int $customerId, int $page = 1): ?array` 📋

Получает список заказов клиента с пагинацией.

* **Параметры**:
  * `$customerId` (int): ID клиента.
  * `$page` (int): Номер страницы (по умолчанию 1).
* **Возвращает**: Массив — ответ от API, содержащий заказы и информацию о пагинации.
* **Логика**:
  * Формирует фильтр с `apiKey`, `customerId`, `page` и `limit=100`.
  * Отправляет GET-запрос на `/api/v5/orders`.
*   **Пример**:

    ```php
    $response = $customerManager->getCustomerOrders(123, 1);
    print_r($response);
    ```

***

#### 9. `getCustomerById(int $customerId): ?array` 👤

Получает информацию о клиенте по его ID.

* **Параметры**:
  * `$customerId` (int): ID клиента.
* **Возвращает**: Массив с ключами:
  * `result` (bool): Успешность поиска (`true`/`false`).
  * `data` (array): Данные клиента (при успехе).
  * `text_error` (string): Сообщение об ошибке (при неудаче).
* **Логика**:
  * Формирует фильтр с `apiKey` и `ids` (массив с `$customerId`).
  * Отправляет GET-запрос на `/api/v5/customers`.
  * Если клиент найден (`totalPageCount > 0`), возвращает первого клиента из списка.
  * Иначе возвращает ошибку "Клиент не найден".
*   **Пример**:

    ```php
    $response = $customerManager->getCustomerById(123);
    print_r($response);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Реализуйте надежную обработку ошибок в `RequestHandler` и проверяйте ответы API.

    ```php
    if (!$response['success']) {
        error_log('CRM API error: ' . $response['message']);
        return ['result' => false, 'text_error' => $response['message']];
    }
    ```
* **Валидация параметров**:
  * Проверяйте, что `$crmDomain` — действительный URL, а `$crmKey` — непустая строка.
  * Убедитесь, что `$customerId` — положительное число.
  * Валидируйте `$data` в методах поиска (`searchCustomer`, `searchCustomersPhoneEmail`) на наличие ожидаемых ключей (`name`, `phone`, `email`).
  * Проверяйте, что `$page` — положительное число в `getCustomerOrders`.
* **Безопасность**:
  * Храните `$crmKey` в безопасном месте (например, в переменных окружения).
  * Очищайте входные данные (например, телефон в `searchCustomersPhoneEmail`) для предотвращения ошибок.
* **Оптимизация**:
  * Кэшируйте данные о клиентах и заказах, если они редко меняются, чтобы снизить количество API-запросов.
  * Обрабатывайте пагинацию эффективно, избегая лишних запросов, если `totalPageCount` невелико.
*   **Логирование**: Внедрите логирование запросов к API и ошибок для упрощения отладки.

    ```php
    error_log('Searching customer with filter: ' . json_encode($data));
    ```
* **Работа с пагинацией**:
  * В `searchCustomers` и `getCustomerOrders` проверяйте `pagination.totalPageCount`, чтобы избежать ненужных запросов.
  * Рассмотрите возможность возврата полной информации о клиентах (а не только ID) в `searchCustomers`, если это требуется.

***

### 🐛 Устранение неполадок

* **Ошибка API CRM**:
  * Проверьте, что `$crmDomain` и `$crmKey` корректны и API доступен.
  * Убедитесь, что `RequestHandler` правильно обрабатывает HTTP-запросы и возвращает ответы.
  * Проверьте формат `$data` в методах `editCustomer`, `searchCustomer`, `searchCustomers`.
* **Клиент не найден**:
  * Убедитесь, что фильтры в `$data` (`name`, `phone`, `email`) соответствуют данным в CRM.
  * Проверьте, что `$customerId` существует в CRM для методов `editCustomer`, `getCustomerById`, `getCustomerOrders`.
* **Проблемы с пагинацией**:
  * Проверьте, что `pagination.totalPageCount` возвращается в ответе API и корректно обрабатывается.
  * Убедитесь, что `limit=100` поддерживается API CRM, или настройте его согласно документации.
* **Ошибки поиска по телефону/email**:
  * Проверьте, что `phone` и `email` в `$data` соответствуют формату, ожидаемому CRM (например, телефон в `name`).
  * Убедитесь, что API поддерживает поиск по `name` для телефона и `email` для почты.
* **Некорректный ответ API**:
  * Проверьте структуру ответа (`customers`, `pagination`) на соответствие документации API.
  * Убедитесь, что API возвращает ожидаемые поля (`id`, `firstName`, и т.д.).

***
