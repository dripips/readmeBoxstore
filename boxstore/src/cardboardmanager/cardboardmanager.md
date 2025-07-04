---
icon: php
---

# CardboardManager

## 📦 Документация по классу CardboardManager

Добро пожаловать в документацию по классу `CardboardManager`! 🎉 Этот PHP-класс предназначен для управления запасами картона, ценами и операциями поставки в складской системе. Он взаимодействует с базой данных через PDO и предоставляет мощный набор методов для выполнения различных задач, связанных с картоном. Ниже вы найдете подробное руководство по использованию, описание методов и лучшие практики внедрения.

***

### 🚀 Обзор

Класс `CardboardManager` находится в пространстве имен `CardboardManager` и создан для выполнения следующих операций:

* Обновление цен для клиентов и производства 📈
* Управление запасами картона (списание, поставки, количества) 📦
* Получение детальной информации о запасах и ценах 🔍
* Ведение логов для аудита 📝
* Добавление новых типов картона в систему ➕

Класс использует подключение PDO для безопасного и эффективного взаимодействия с базой данных. Каждый метод разработан интуитивно, с четкой обработкой ошибок и сообщениями об успехе.

***

### 🛠️ Установка и настройка

Чтобы использовать класс `CardboardManager`, выполните следующие шаги:

1.  **Подключите класс**: Убедитесь, что файл класса включен в ваш PHP-проект.

    ```php
    require_once 'path/to/CardboardManager.php';
    ```
2.  **Настройте подключение PDO**: Создайте экземпляр PDO для подключения к базе данных.

    ```php
    $dsn = 'mysql:host=localhost;dbname=your_database';
    $username = 'your_username';
    $password = 'your_password';
    $pdo = new PDO($dsn, $username, $password);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    ```
3.  **Создайте экземпляр класса**: Передайте экземпляр PDO в конструктор `CardboardManager`.

    ```php
    use CardboardManager\CardboardManager;
    $cardboardManager = new CardboardManager($pdo);
    ```
4. **Используйте методы**: Вызывайте нужные методы для выполнения операций (подробности ниже).

***

### 📋 Методы и использование

Ниже приведен полный список методов класса `CardboardManager`, их назначение, параметры и примеры использования.

#### 1. `__construct(PDO $pdo)` 🛠️

Инициализирует класс с подключением PDO.

* **Параметры**:
  * `$pdo` (PDO): Экземпляр PDO для работы с базой данных.
* **Возвращает**: Ничего
*   **Пример**:

    ```php
    $pdo = new PDO('mysql:host=localhost;dbname=warehouse', 'user', 'pass');
    $cardboardManager = new CardboardManager($pdo);
    ```

***

#### 2. `updateClientPrice(array $cardboards)` 💸

Обновляет цену за лист для клиентов в таблице `cardboardPricingForClients`.

* **Параметры**:
  * `$cardboards` (array): Массив картона, каждый элемент содержит `id` (ID картона) и `price` (цена за лист).
* **Возвращает**: Строка (сообщение об успехе или ошибке)
*   **Пример**:

    ```php
    $cardboards = [
        ['id' => 1, 'price' => '10.50'],
        ['id' => 2, 'price' => '12.00']
    ];
    echo $cardboardManager->updateClientPrice($cardboards);
    // Вывод: "Обновление успешно завершено."
    ```

***

#### 3. `updateProductionPrice(array $cardboards)` 🏭

Обновляет цену за лист для производства в таблице `cardboardPricing`.

* **Параметры**:
  * `$cardboards` (array): Массив картона, каждый элемент содержит `id` (ID картона) и `price` (цена за лист).
* **Возвращает**: Строка (сообщение об успехе или ошибке)
*   **Пример**:

    ```php
    $cardboards = [
        ['id' => 1, 'price' => '9.75'],
        ['id' => 2, 'price' => '11.25']
    ];
    echo $cardboardManager->updateProductionPrice($cardboards);
    // Вывод: "Обновление успешно завершено."
    ```

***

#### 4. `writeOffCardboard($cardboardID, $quantityToWriteOff)` 📉

Списывает указанное количество картона со склада.

* **Параметры**:
  * `$cardboardID` (int): ID картона.
  * `$quantityToWriteOff` (int): Количество для списания.
* **Возвращает**: Строка (сообщение об успехе или ошибке)
*   **Пример**:

    ```php
    echo $cardboardManager->writeOffCardboard(1, 50);
    // Вывод: "Листы успешно списаны. Остаток: 150."
    ```

***

#### 5. `getCardboardStock()` 📊

Получает детальную информацию о запасах картона, включая количества, цены и цвета.

* **Параметры**: Нет
* **Возвращает**: Массив с информацией о запасах или сообщение об ошибке
*   **Пример**:

    ```php
    $stock = $cardboardManager->getCardboardStock();
    print_r($stock);
    // Вывод: Массив с данными о запасах
    ```

***

#### 6. `getPriceForClients()` 💰

Извлекает информацию о ценах для клиентов из таблицы `cardboardStorePricer`.

* **Параметры**: Нет
* **Возвращает**: Массив с данными о ценах или сообщение об ошибке
*   **Пример**:

    ```php
    $prices = $cardboardManager->getPriceForClients();
    print_r($prices);
    // Вывод: Массив с данными о ценах для клиентов
    ```

***

#### 7. `getCardboardIdByColorAndSizes($cardboardColor, $lSheet, $wSheet)` 🔎

Получает ID картона по его цвету и размерам.

* **Параметры**:
  * `$cardboardColor` (string): Цвет картона.
  * `$lSheet` (int): Длина листа.
  * `$wSheet` (int): Ширина листа.
* **Возвращает**: Массив с соответствующими ID или сообщение об ошибке
*   **Пример**:

    ```php
    $ids = $cardboardManager->getCardboardIdByColorAndSizes('Белый', 1000, 700);
    print_r($ids);
    // Вывод: Массив с ID картона
    ```

***

#### 8. `updateCardboardAmount(array $cardboards)` 🔄

Обновляет плановое и фактическое количество картона в таблице `cardboardAmount`.

* **Параметры**:
  * `$cardboards` (array): Массив картона, каждый элемент содержит `id`, `plannedQuantity` и `actualQuantity`.
* **Возвращает**: Строка (сообщение об успехе или ошибке)
*   **Пример**:

    ```php
    $cardboards = [
        ['id' => 1, 'plannedQuantity' => 200, 'actualQuantity' => 180],
        ['id' => 2, 'plannedQuantity' => 150, 'actualQuantity' => 140]
    ];
    echo $cardboardManager->updateCardboardAmount($cardboards);
    // Вывод: "Обновление успешно завершено."
    ```

***

#### 9. `getPricePerSheet($cardboardID)` 💵

Получает цену за лист для указанного картона.

* **Параметры**:
  * `$cardboardID` (int): ID картона.
* **Возвращает**: Цена (float) или null/сообщение об ошибке
*   **Пример**:

    ```php
    $price = $cardboardManager->getPricePerSheet(1);
    echo $price;
    // Вывод: 10.50
    ```

***

#### 10. `getCardboardDetails()` 📋

Извлекает данные из таблицы `cardboardStore`, включая информацию о цвете и сортировке для админа.

* **Параметры**: Нет
* **Возвращает**: Массив с данными или сообщение об ошибке
*   **Пример**:

    ```php
    $details = $cardboardManager->getCardboardDetails();
    print_r($details);
    // Вывод: Массив с данными о картоне
    ```

***

#### 11. `getCardboardDetailsForClients()` 📋

Извлекает данные из таблицы `cardboardStorePricer` для цен клиентов.

* **Параметры**: Нет
* **Возвращает**: Массив с данными или сообщение об ошибке
*   **Пример**:

    ```php
    $details = $cardboardManager->getCardboardDetailsForClients();
    print_r($details);
    // Вывод: Массив с данными о ценах для клиентов
    ```

***

#### 12. `getCardboardSupplies()` 🚚

Получает все записи о поставках картона из таблицы `cardboardSupply`.

* **Параметры**: Нет
* **Возвращает**: Массив с записями о поставках или сообщение об ошибке
*   **Пример**:

    ```php
    $supplies = $cardboardManager->getCardboardSupplies();
    print_r($supplies);
    // Вывод: Массив с данными о поставках
    ```

***

#### 13. `pricerCardboard($cardboards)` 💲

Обновляет цены для клиентов в таблице `cardboardStorePricer` в рамках транзакции.

* **Параметры**:
  * `$cardboards` (array): Массив картона, каждый элемент содержит `id` и `priceSheet`.
* **Возвращает**: Строка (сообщение об успехе или ошибке)
*   **Пример**:

    ```php
    $cardboards = [
        ['id' => 1, 'priceSheet' => 12.00],
        ['id' => 2, 'priceSheet' => 14.50]
    ];
    echo $cardboardManager->pricerCardboard($cardboards);
    // Вывод: "Цены успешно изменены!"
    ```

***

#### 14. `addNewCardboard($name, $length, $width, $color)` ➕

Добавляет новую запись о картоне в таблицу `cardboardStore`.

* **Параметры**:
  * `$name` (string): Название картона.
  * `$length` (int): Длина листа.
  * `$width` (int): Ширина листа.
  * `$color` (string): Цвет картона.
* **Возвращает**: Строка (сообщение об успехе или ошибке)
*   **Пример**:

    ```php
    echo $cardboardManager->addNewCardboard('Премиум Белый', 1200, 800, 'Белый');
    // Вывод: "Новый картон успешно добавлен."
    ```

***

#### 15. `supplyCardboard(array $cardboards)` 📦

Добавляет новые количества поставки на склад, обновляя таблицы `cardboardAmount` и `cardboardSupply`.

* **Параметры**:
  * `$cardboards` (array): Массив картона, каждый элемент содержит `id` и `supply`.
* **Возвращает**: Строка (сообщение об успехе или ошибке)
*   **Пример**:

    ```php
    $cardboards = [
        ['id' => 1, 'supply' => 100],
        ['id' => 2, 'supply' => 50]
    ];
    echo $cardboardManager->supplyCardboard($cardboards);
    // Вывод: "Склад успешно пополнен!"
    ```

***

#### 16. `getDoubleCardboardById($cardboardId, $doubleColor)` 🔍

Получает картон с соответствующими размерами и указанным двойным цветом.

* **Параметры**:
  * `$cardboardId` (int): ID эталонного картона.
  * `$doubleColor` (int): ID двойного цвета.
* **Возвращает**: Массив с соответствующими картонами или пустой массив
*   **Пример**:

    ```php
    $cardboards = $cardboardManager->getDoubleCardboardById(1, 2);
    print_r($cardboards);
    // Вывод: Массив с соответствующими картонами
    ```

***

#### 17. `logCardboardAction($count, $color, $l_sheet, $w_sheet, $comment, $action, $RCRM, $nocodb_id)` 📝

Записывает действие, связанное с картоном, в таблицу `logs`.

* **Параметры**:
  * `$count` (int): Количество.
  * `$color` (string): Цвет картона.
  * `$l_sheet` (int): Длина листа.
  * `$w_sheet` (int): Ширина листа.
  * `$comment` (string): Дополнительный комментарий.
  * `$action` (string): Тип действия (например, 'supply', 'write-off').
  * `$RCRM` (string): Идентификатор RCRM.
  * `$nocodb_id` (string): Идентификатор NocoDB.
* **Возвращает**: Массив с статусом `success` и, при необходимости, сообщением об ошибке
*   **Пример**:

    ```php
    $result = $cardboardManager->logCardboardAction(100, 'Белый', 1000, 700, 'Новая поставка', 'supply', 'RCRM123', 'NOCO456');
    print_r($result);
    // Вывод: ['success' => true]
    ```

***

#### 18. `getLogs($action, $data, $flag = false)` 📜

Получает логи, отфильтрованные по действию и дополнительным критериям.

* **Параметры**:
  * `$action` (string): Тип действия для фильтрации логов (например, 'supply').
  * `$data` (array): Критерии фильтрации (например, диапазон дат, данные картона, RCRM).
  * `$flag` (bool): Если true, агрегирует результаты по типу картона.
* **Возвращает**: Массив с статусом `success`, данными (`data`) или сообщением об ошибке (`message`)
*   **Пример**:

    ```php
    $data = [
        'filter' => [
            'date' => ['start' => '2025-01-01', 'end' => '2025-12-31'],
            'cardboard' => ['color' => 'Белый', 'lSheet' => 1000, 'wSheet' => 700],
            'RCRM' => 'RCRM123'
        ]
    ];
    $logs = $cardboardManager->getLogs('supply', $data);
    print_r($logs);
    // Вывод: Массив с отфильтрованными логами
    ```

***

### ⚠️ Лучшие практики

* **Обработка ошибок**: Всегда проверяйте возвращаемые значения методов, так как они могут содержать сообщения об ошибках.
* **Транзакции**: Методы, такие как `supplyCardboard` и `pricerCardboard`, используют транзакции для обеспечения целостности данных. Убедитесь, что ваша база данных поддерживает транзакции (например, InnoDB для MySQL).
* **Валидация входных данных**: Проверяйте входные данные перед передачей в методы, чтобы избежать SQL-инъекций или проблем с некорректными данными.
* **Логирование**: Используйте `logCardboardAction` для ведения аудита всех значимых действий.
* **Схема базы данных**: Убедитесь, что схема вашей базы данных соответствует ожидаемым таблицам (`cardboardStore`, `cardboardAmount`, `cardboardPricing` и т.д.), чтобы избежать ошибок в запросах.

***

### 🐛 Устранение неполадок

* **Ошибки PDO**: Если возникают исключения PDO, проверьте данные подключения к базе данных и убедитесь, что расширение PDO включено в PHP.
* **Отсутствие записей**: Если методы возвращают ошибки "не найдено", проверьте, существуют ли соответствующие записи в базе данных.
* **Ошибки транзакций**: Убедитесь, что движок базы данных поддерживает транзакции и нет конфликтующих запросов.
