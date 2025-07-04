---
icon: php
---

# OrderManager

## 📦 Документация по классу OrderManager

Добро пожаловать в документацию по классу `OrderManager`! 🎉 Этот PHP-класс, расположенный в пространстве имен `RCRM`, предназначен для управления заказами в CRM-системе через API, а также для формирования данных об упаковке и получателях для службы доставки СДЭК. Класс использует трейт `RequestHandler` для отправки HTTP-запросов и предоставляет методы для создания, редактирования, получения информации о заказах, а также для генерации свойств коробок и упаковок. Ниже вы найдете подробное руководство по использованию, описание методов и рекомендации по внедрению.

***

### 🚀 Обзор

Класс `OrderManager` разработан для выполнения следующих задач:

* Создание, редактирование и получение информации о заказах через API CRM-системы 📋
* Генерация свойств коробок на основе производственных и клиентских расчетов 📦
* Формирование данных о получателях заказов для СДЭК 🚚
* Создание структуры упаковок для доставки с учетом типа коробки, размеров и количества 🧩
* Подготовка данных для расчета тарифов доставки СДЭК 📏

Класс использует трейт `RequestHandler` для отправки GET- и POST-запросов к API CRM, поддерживает интеграцию с калькуляцией коробок (например, через `BoxCalculation`) и предоставляет гибкие методы для работы с заказами и логистикой.

***

### 🛠️ Установка и настройка

Чтобы использовать класс `OrderManager`, выполните следующие шаги:

1.  **Подключите класс и трейт**: Убедитесь, что файлы класса `OrderManager` и трейта `RequestHandler` включены в ваш PHP-проект.

    ```php
    require_once 'path/to/OrderManager.php';
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
    use RCRM\OrderManager;

    $crmDomain = 'https://example-crm.com';
    $crmKey = 'your-api-key';
    $orderManager = new OrderManager($crmDomain, $crmKey);
    ```
4. **Используйте методы**: Вызывайте методы для управления заказами, генерации свойств или подготовки данных для СДЭК.

***

### 📋 Методы и использование

Класс `OrderManager` предоставляет публичные и приватные методы для работы с заказами и логистикой. Ниже описаны все методы.

#### 1. `__construct(string $crmDomain, string $crmKey)` 🛠️

Инициализирует класс, сохраняя домен CRM и API-ключ.

* **Параметры**:
  * `$crmDomain` (string): Домен CRM-системы (например, `https://example-crm.com`).
  * `$crmKey` (string): API-ключ для аутентификации.
* **Возвращает**: Ничего
*   **Пример**:

    ```php
    $orderManager = new OrderManager('https://example-crm.com', 'your-api-key');
    ```

***

#### 2. `createOrder(array $data): ?array` 📋

Создает новый заказ в CRM через API.

* **Параметры**:
  * `$data` (array): Данные заказа (поля зависят от API CRM).
* **Возвращает**: `?array` — ответ от API (или `null` при ошибке).
* **Логика**:
  * Добавляет `apiKey` в `$data`.
  * Отправляет POST-запрос на `/api/v5/orders/create` с помощью `setPostRequest`.
*   **Пример**:

    ```php
    $data = [
        'name' => 'Order #123',
        'customerId' => 456,
        // Другие поля заказа
    ];
    $response = $orderManager->createOrder($data);
    print_r($response);
    ```

***

#### 3. `editOrder(string $number, array $data): ?array` 📋

Редактирует существующий заказ в CRM по номеру.

* **Параметры**:
  * `$number` (string): Номер заказа (очищается от букв, остаются только цифры).
  * `$data` (array): Данные для обновления заказа.
* **Возвращает**: `?array` — ответ от API (или `null` при ошибке).
* **Логика**:
  * Очищает `$number` от нечисловых символов с помощью `preg_replace("/[^0-9]/", '', $number)`.
  * Добавляет `apiKey` в `$data`.
  * Отправляет POST-запрос на `/api/v5/orders/$number/edit`.
*   **Пример**:

    ```php
    $data = [
        'status' => 'confirmed',
        // Другие поля для обновления
    ];
    $response = $orderManager->editOrder('ORD123', $data);
    print_r($response);
    ```

***

#### 4. `getOrderByFilter(array $data): ?array` 📋

Получает список заказов из CRM по фильтру.

* **Параметры**:
  * `$data` (array): Параметры фильтрации (зависят от API CRM).
* **Возвращает**: `?array` — ответ от API (или `null` при ошибке).
* **Логика**:
  * Добавляет `apiKey` и `limit=100` в `$data`.
  * Отправляет GET-запрос на `/api/v5/orders` с помощью `sendGetRequest`.
*   **Пример**:

    ```php
    $data = [
        'status' => 'pending',
        // Другие параметры фильтра
    ];
    $response = $orderManager->getOrderByFilter($data);
    print_r($response);
    ```

***

#### 5. `getOrderInfo(string $number): ?array` 📋

Получает информацию о заказе по номеру.

* **Параметры**:
  * `$number` (string): Номер заказа (очищается от букв).
* **Возвращает**: Массив с ключами:
  * `result` (bool): Успешность запроса (`true`/`false`).
  * `data` (array): Данные заказа (при успехе).
  * `text_error` (string): Сообщение об ошибке (при неудаче).
* **Логика**:
  * Очищает `$number` от нечисловых символов.
  * Формирует GET-запрос на `/api/v5/orders/$number` с параметрами `by=id` и `apiKey`.
  * Если ответ неуспешен (`success=false`), возвращает ошибку с сообщением "Заказ не найден".
  * При успехе возвращает данные заказа (`order`).
*   **Пример**:

    ```php
    $response = $orderManager->getOrderInfo('ORD123');
    print_r($response);
    ```

***

#### 6. `generateProperties($calculationProduction, $calculationCustomer): array` 📦

Генерирует массив свойств коробки на основе производственных и клиентских расчетов.

* **Параметры**:
  * `$calculationProduction` (array): Данные производственной калькуляции (например, из `BoxCalculation`).
  * `$calculationCustomer` (array): Данные клиентской калькуляции.
* **Возвращает**: Массив свойств коробки (код, название, значение).
* **Логика**:
  * Извлекает данные из `$calculationProduction['data']` и `$calculationCustomer['data']`.
  * Формирует массив свойств, включая затраты, размеры, тип коробки, количество и т.д.
  * Округляет числовые значения (например, `costMatterOne`) до двух знаков после запятой.
  * Рассчитывает производные значения, такие как `sheetsOnOneBox`.
*   **Пример**:

    ```php
    $calculationProduction = ['data' => ['costMatterOne' => 10.123, 'typeBox' => '427', /* ... */]];
    $calculationCustomer = ['data' => ['costNetOne' => 15.456]];
    $properties = $orderManager->generateProperties($calculationProduction, $calculationCustomer);
    print_r($properties);
    ```

***

#### 7. `getRecipientInfo($orderInfo): array` 🚚

Формирует данные о получателе заказа для СДЭК.

* **Параметры**:
  * `$orderInfo` (array): Информация о заказе из CRM.
* **Возвращает**: Массив с данными получателя (`name`, `phones`, `company`, `tin`, `email`).
* **Логика**:
  * Формирует ФИО из `customFields.fio_poluchatelia1` или комбинации `lastName`, `firstName`, `patronymic`.
  * Извлекает компанию (`legalName`) и ИНН (`INN`) из `customer.contragent`.
  * Очищает номер телефона от нечисловых символов или использует значение по умолчанию (`+79999999999`).
  * Добавляет email, если он присутствует в `$orderInfo`.
*   **Пример**:

    ```php
    $orderInfo = [
        'customFields' => ['fio_poluchatelia1' => 'Иванов Иван Иванович'],
        'phone' => '+79991234567',
        'email' => 'ivan@example.com'
    ];
    $recipient = $orderManager->getRecipientInfo($orderInfo);
    print_r($recipient);
    ```

***

#### 8. `getPackaging(array $items): array` 🧩

Формирует структуру упаковок для СДЭК на основе элементов заказа.

* **Параметры**:
  * `$items` (array): Массив элементов заказа (содержит свойства коробок).
* **Возвращает**: Массив упаковок с параметрами (`packageId`, `weight`, `length`, `width`, `height`, `amountBoxes`, `cost`).
* **Логика**:
  * Итерируется по `$items`, вызывая `getPackagingItem` для каждого элемента.
  * Собирает все упаковки в единый массив, увеличивая `packageId` для каждой новой упаковки.
*   **Пример**:

    ```php
    $items = [
        [
            'properties' => ['typeBox' => '427', 'lGab' => 600, 'wGab' => 400, 'typeCarton' => 1.5, 'weightOneBox' => 0.5],
            'quantity' => 150,
            'initialPrice' => 100
        ]
    ];
    $packaging = $orderManager->getPackaging($items);
    print_r($packaging);
    ```

***

#### 9. `getPackagingItem(array $item, int $packageId): array` (приватный) 🧩

Формирует упаковки для одного элемента заказа.

* **Параметры**:
  * `$item` (array): Данные элемента заказа (свойства, количество, цена).
  * `$packageId` (int): Начальный ID упаковки.
* **Возвращает**: Массив упаковок для элемента.
* **Логика**:
  * Определяет максимальное количество коробок в упаковке через `getMaxInPackage`.
  * Рассчитывает габариты упаковки через `getDimensions`.
  * Разбивает количество коробок (`quantity`) на упаковки через `getAmountInPackaging`.
  * Для каждой упаковки формирует данные, включая высоту (через `getHeight`), вес и стоимость.
* **Примечание**: Вызывается внутри `getPackaging`.

***

#### 10. `getMaxInPackage(string $typeBox): int` (приватный) 📏

Определяет максимальное количество коробок в одной упаковке в зависимости от типа коробки.

* **Параметры**:
  * `$typeBox` (string): Тип коробки (например, `201`, `427`).
* **Возвращает**: `int` — максимальное количество коробок.
* **Логика**:
  * Возвращает 50 для типов `201`, `215`, `330` и 100 для остальных.
* **Примечание**: Вызывается внутри `getPackagingItem`.

***

#### 11. `getDimensions(array $item, int $amountInPackaging): array` (приватный) 📏

Рассчитывает габариты упаковки для элемента.

* **Параметры**:
  * `$item` (array): Данные элемента заказа.
  * `$amountInPackaging` (int): Максимальное количество коробок в упаковке.
* **Возвращает**: Массив с габаритами (`length`, `width`, `height`).
* **Логика**:
  * Извлекает длину (`lGab`) и ширину (`wGab`) из свойств элемента.
  * Для типов `201`, `215`, `330` делит длину на 2.
  * Рассчитывает высоту как `quantity * typeCarton`.
* **Примечание**: Вызывается внутри `getPackagingItem`.

***

#### 12. `getAmountInPackaging(int $quantity, int $amountInPackaging): array` (приватный) 📦

Разбивает общее количество коробок на упаковки.

* **Параметры**:
  * `$quantity` (int): Общее количество коробок.
  * `$amountInPackaging` (int): Максимальное количество коробок в одной упаковке.
* **Возвращает**: Массив количеств коробок для каждой упаковки.
* **Логика**:
  * Итерируется, пока `$quantity > 0`, добавляя в массив минимальное из `$quantity` и `$amountInPackaging`.
* **Примечание**: Вызывается внутри `getPackagingItem`.

***

#### 13. `getHeight(string $boxType, int $amount, $cartonType)` (приватный) 📏

Рассчитывает высоту упаковки с учетом типа коробки.

* **Параметры**:
  * `$boxType` (string): Тип коробки.
  * `$amount` (int): Количество коробок в упаковке.
  * `$cartonType` (float|string): Толщина картона.
* **Возвращает**: Высота упаковки (число).
* **Логика**:
  * Рассчитывает базовую высоту как `$amount * $cartonType`.
  * Удваивает высоту для типов `201`, `215`, `330`, `300`, `6`, `1006`.
* **Примечание**: Вызывается внутри `getPackagingItem`.

***

#### 14. `getTariffPackagins($packagins): array` 📦

Подготавливает данные упаковок для расчета тарифов СДЭК.

* **Параметры**:
  * `$packagins` (array): Массив упаковок (из `getPackaging`).
* **Возвращает**: Массив упаковок с параметрами для тарифов (`weight`, `length`, `width`, `height`).
* **Логика**:
  * Для каждой упаковки преобразует габариты в сантиметры (делит на 10, округляя вниз).
  * Устанавливает высоту минимум в 1 см.
  * Рассчитывает вес как `weight * amountBoxes`.
*   **Пример**:

    ```php
    $packagins = [
        ['packageId' => 1, 'weight' => 0.5, 'length' => 600, 'width' => 400, 'height' => 15, 'amountBoxes' => 100]
    ];
    $tariffPackagins = $orderManager->getTariffPackagins($packagins);
    print_r($tariffPackagins);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Реализуйте надежную обработку ошибок в `RequestHandler` и проверяйте ответы API.

    ```php
    if (!$response['success']) {
        error_log('CRM API error: ' . $response['message']);
        return null;
    }
    ```
* **Валидация параметров**:
  * Проверяйте, что `$crmDomain` — действительный URL, а `$crmKey` — непустая строка.
  * Убедитесь, что `$number` содержит цифры для методов `editOrder` и `getOrderInfo`.
  * Валидируйте `$calculationProduction` и `$calculationCustomer` на наличие необходимых ключей (`data.costMatterOne`, `data.typeBox` и т.д.).
  * Проверяйте, что `$items` содержит ожидаемые свойства (`typeBox`, `lGab`, `wGab`, `typeCarton`, `weightOneBox`).
* **Безопасность**:
  * Храните `$crmKey` в безопасном месте (например, в переменных окружения).
  * Очищайте входные данные (например, номер телефона в `getRecipientInfo`) для предотвращения ошибок.
* **Оптимизация**:
  * Кэшируйте данные о заказах, если они редко меняются, чтобы снизить количество API-запросов.
  * Объединяйте запросы к API, если это поддерживается CRM.
* **Логирование**: Внедрите логирование запросов к API и ошибок для упрощения отладки.
* **Интеграция с калькуляцией**:
  * Убедитесь, что `$calculationProduction` и `$calculationCustomer` получены из `BoxCalculation` и содержат все необходимые поля.
  * Проверяйте совместимость типов коробок с `getMaxInPackage` и `getHeight`.

***

### 🐛 Устранение неполадок

* **Ошибка API CRM**:
  * Проверьте, что `$crmDomain` и `$crmKey` корректны и API доступен.
  * Убедитесь, что `RequestHandler` правильно обрабатывает HTTP-запросы и возвращает ответы.
  * Проверьте формат `$data` в методах `createOrder`, `editOrder`, `getOrderByFilter`.
* **Некорректный номер заказа**:
  * Убедитесь, что `$number` содержит цифры после очистки в `editOrder` и `getOrderInfo`.
  * Проверьте, существует ли заказ с указанным номером в CRM.
* **Отсутствие данных в `$calculationProduction` или `$calculationCustomer`**:
  * Проверьте, что данные калькуляции содержат все ожидаемые ключи (`data.costMatterOne`, `data.typeBox` и т.д.).
  * Убедитесь, что `BoxCalculation` возвращает корректные результаты.
* **Ошибки формирования упаковок**:
  * Проверьте, что `$items` содержит все необходимые свойства (`typeBox`, `lGab`, `wGab`, `typeCarton`, `weightOneBox`, `quantity`, `initialPrice`).
  * Убедитесь, что `typeBox` поддерживается в `getMaxInPackage` и `getHeight`.
* **Некорректные габариты**:
  * Проверьте, что `lGab`, `wGab`, `typeCarton` — положительные числа.
  * Убедитесь, что высота (`height`) в `getTariffPackagins` корректно преобразуется в сантиметры.

***
