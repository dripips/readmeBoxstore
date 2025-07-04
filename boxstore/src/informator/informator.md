---
icon: php
---

# Informator

## 📊 Документация по классу Informator

Добро пожаловать в документацию по классу `Informator`! 🎉 Этот PHP-класс, расположенный в пространстве имен `Informator`, предназначен для взаимодействия с несколькими базами данных (cloud\_muul, cardboard\_store, muul\_formulas\_drawings) через PDO, предоставляя методы для получения данных о промокодах, тиражах, наценках, картоне, цветах, типах коробок, а также расчета размеров упаковок и рабочих областей. Ниже вы найдете подробное руководство по использованию, описание методов и рекомендации по внедрению.

***

### 🚀 Обзор

Класс `Informator` разработан для выполнения следующих задач:

* Получение данных из различных баз данных (промокоды, тиражи, наценки, картон, цвета, типы коробок) 📋
* Поиск рабочей области листа картона по цвету и размерам 🔍
* Расчет внутренних размеров коробок для негенеративных форм 📏
* Определение размеров упаковки для различных типов коробок 📦
* Поддержка режима разработки (`dev`) для отображения скрытых данных ⚙️

Класс использует PDO для безопасного взаимодействия с тремя базами данных, инициализируя отдельные подключения для каждой. Он включает справочник цветов (`singleCodes`) для преобразования устаревших кодов и предоставляет гибкие методы для расчета размеров упаковок с учетом типа коробки и количества.

***

### 🛠️ Установка и настройка

Чтобы использовать класс `Informator`, выполните следующие шаги:

1.  **Подключите класс**: Убедитесь, что файл класса включен в ваш PHP-проект.

    ```php
    require_once 'path/to/Informator.php';
    ```
2. **Настройте конфигурацию**:
   * Подготовьте массив конфигурации с данными для подключения к базам данных (`cloud_muul`, `cardboard_store`, `muul_formulas_drawings`).
   * Каждая база данных должна содержать ключи `host`, `database`, и `users.read` с `username` и `password`.
3.  **Создайте экземпляр класса**: Передайте конфигурацию и, при необходимости, флаг режима разработки (`dev`).

    ```php
    use Informator\Informator;
    $config = [
        'databases' => [
            'cloud_muul' => [
                'host' => 'localhost',
                'database' => 'cloud_muul',
                'users' => [
                    'read' => [
                        'username' => 'user',
                        'password' => 'pass'
                    ]
                ]
            ],
            'cardboard_store' => [
                'host' => 'localhost',
                'database' => 'cardboard_store',
                'users' => [
                    'read' => [
                        'username' => 'user',
                        'password' => 'pass'
                    ]
                ]
            ],
            'muul_formulas_drawings' => [
                'host' => 'localhost',
                'database' => 'muul_formulas',
                'users' => [
                    'read' => [
                        'username' => 'user',
                        'password' => 'pass'
                    ]
                ]
            ]
        ]
    ];
    $informator = new Informator($config, $dev = false);
    ```
4. **Используйте методы**: Вызывайте методы для получения данных или расчета размеров (подробности ниже).

***

### 📋 Методы и использование

Ниже приведен полный список методов класса `Informator`, их назначение, параметры и примеры использования.

#### 1. `__construct(array $config, $dev = false)` 🛠️

Инициализирует класс, устанавливая конфигурацию и создавая PDO-подключения к трем базам данных.

* **Параметры**:
  * `$config` (array): Конфигурация с данными для баз данных.
  * `$dev` (bool): Флаг режима разработки (по умолчанию `false`).
* **Возвращает**: Ничего
* **Исключения**: `PDOException` при ошибке подключения к базе данных.
*   **Пример**:

    ```php
    $informator = new Informator($config, $dev = true);
    ```

***

#### 2. `createPdoCloudMuul()` 🔗

(Приватный метод) Создает PDO-подключение к базе данных `cloud_muul`.

* **Возвращает**: Ничего (устанавливает `$pdoCloudMuul`).
* **Исключения**: `PDOException` при ошибке подключения.
* **Примечание**: Вызывается автоматически в конструкторе.

***

#### 3. `createPdoCardboard()` 🔗

(Приватный метод) Создает PDO-подключение к базе данных `cardboard_store`.

* **Возвращает**: Ничего (устанавливает `$pdoCardboardStore`).
* **Исключения**: `PDOException` при ошибке подключения.
* **Примечание**: Вызывается автоматически в конструкторе.

***

#### 4. `createPdoMuulFormulas()` 🔗

(Приватный метод) Создает PDO-подключение к базе данных `muul_formulas_drawings`.

* **Возвращает**: Ничего (устанавливает `$pdoMuulFormulas`).
* **Исключения**: `PDOException` при ошибке подключения.
* **Примечание**: Вызывается автоматически в конструкторе.

***

#### 5. `getPromocodes()` 📋

Получает список всех промокодов из таблицы `calculator_promocodes` в базе `cloud_muul`.

* **Параметры**: Нет
* **Возвращает**: Массив с ключами `result` (true) и `data` (список промокодов).
*   **Пример**:

    ```php
    try {
        $promocodes = $informator->getPromocodes();
        print_r($promocodes['data']);
        // Вывод: Массив промокодов
    } catch (PDOException $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 6. `getCirculation()` 📋

Получает список тиражей из таблицы `circulation` в базе `cloud_muul`.

* **Параметры**: Нет
* **Возвращает**: Массив с ключами `result` (true) и `data` (список тиражей).
*   **Пример**:

    ```php
    try {
        $circulation = $informator->getCirculation();
        print_r($circulation['data']);
        // Вывод: Массив тиражей
    } catch (PDOException $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 7. `getMarkups()` 📋

Получает список наценок из таблицы `type_orders` в базе `cloud_muul`.

* **Параметры**: Нет
* **Возвращает**: Массив с ключами `result` (true) и `data` (список наценок).
*   **Пример**:

    ```php
    try {
        $markups = $informator->getMarkups();
        print_r($markups['data']);
        // Вывод: Массив наценок
    } catch (PDOException $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 8. `getCardboard()` 📦

Получает список картона из таблицы `cardboardStore` в базе `cardboard_store`.

* **Параметры**: Нет
* **Возвращает**: Массив с ключами `result` (true) и `data` (список картона).
*   **Пример**:

    ```php
    try {
        $cardboard = $informator->getCardboard();
        print_r($cardboard['data']);
        // Вывод: Массив данных о картоне
    } catch (PDOException $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 9. `getColors()` 🎨

Получает список доступных цветов картона из таблицы `cardboardStore` в базе `cardboard_store`.

* **Параметры**: Нет
* **Возвращает**: Массив с ключами `result` (true) и `data` (подмассивы `doubleColors`, `colorsRu`, `colorsEn`).
* **Логика**:
  * Исключает цвета `SNOW-WHITE`, `WINE-WHITE`, `WHITE-WHINE` в не-`dev` режиме.
  * Удаляет дубликаты цветов.
*   **Пример**:

    ```php
    try {
        $colors = $informator->getColors();
        print_r($colors['data']['doubleColors']);
        // Вывод: Массив цветов (Ru и En)
    } catch (PDOException $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 10. `getTypes()` 📋

Получает список типов коробок из таблицы `types_boxes` в базе `cloud_muul`.

* **Параметры**: Нет
* **Возвращает**: Массив с ключами `result` (true) и `data` (подмассивы `typesList`, `onlyTypes`).
* **Логика**:
  * Исключает коробки с `onSale = false` в не-`dev` режиме.
*   **Пример**:

    ```php
    try {
        $types = $informator->getTypes();
        print_r($types['data']['typesList']);
        // Вывод: Массив типов коробок
    } catch (PDOException $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 11. `findWorkSize($sheetColor, $sheetLength, $sheetWidth)` 📏

Находит рабочую область листа картона по цвету и размерам.

* **Параметры**:
  * `$sheetColor` (string): Цвет картона (на русском или английском).
  * `$sheetLength` (int): Длина листа.
  * `$sheetWidth` (int): Ширина листа.
* **Возвращает**: Массив с ключами `l` (длина) и `w` (ширина) рабочей области.
* **Логика**:
  * Преобразует устаревшие английские коды цветов в русские (на основе `$singleCodes`).
  * Если лист не найден, возвращает исходные размеры.
*   **Пример**:

    ```php
    try {
        $workSize = $informator->findWorkSize('white', 2300, 1200);
        print_r($workSize);
        // Вывод: ['l' => 2280, 'w' => 1180]
    } catch (PDOException $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 12. `getSizeNonGenerative($model)` 📏

Возвращает внутренние размеры коробок для негенеративных форм (модели 1301, 1302, 1303, 1701).

* **Параметры**:
  * `$model` (int): Код модели коробки.
* **Возвращает**: Массив с ключами `result` и `data` (длина, ширина, высота) или `text_error` при ошибке.
*   **Пример**:

    ```php
    $size = $informator->getSizeNonGenerative(1301);
    print_r($size);
    // Вывод: ['result' => true, 'data' => ['length' => 375, 'width' => 278, 'height' => 308]]
    ```

***

#### 13. `getBoxSize933($lengthCell, $widthCell, $heightBox, $cellNumber)` 📏

Рассчитывает размеры коробки для ложементов типа 933 на основе размеров ячеек и их количества.

* **Параметры**:
  * `$lengthCell` (float): Длина ячейки.
  * `$widthCell` (float): Ширина ячейки.
  * `$heightBox` (float): Высота коробки.
  * `$cellNumber` (int): Количество ячеек.
* **Возвращает**: Массив с ключами `lengthBox`, `widthBox`, `heightBox`.
*   **Пример**:

    ```php
    try {
        $size = $informator->getBoxSize933(50, 40, 100, 4);
        print_r($size);
        // Вывод: ['lengthBox' => 102, 'widthBox' => 82, 'heightBox' => 100]
    } catch (PDOException $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 14. `getPackagingInfo($model, $lengthBox, $widthBox, $heightBox, $amount, $cellNumber = 0)` 📦

Рассчитывает размеры упаковки для указанного типа коробки и количества.

* **Параметры**:
  * `$model` (int): Код модели коробки (1006, 110, 201, 215, 330, 426, 427, 1301, 1302, 1303, 1701, 933).
  * `$lengthBox` (float): Длина коробки.
  * `$widthBox` (float): Ширина коробки.
  * `$heightBox` (float): Высота коробки.
  * `$amount` (int): Количество коробок.
  * `$cellNumber` (int): Количество ячеек (только для модели 933, по умолчанию 0).
* **Возвращает**: Массив с ключами `result` и `data` (длина, ширина, высота упаковки) или `text_error` при ошибке.
* **Логика**:
  * Учитывает толщину картона (1.5 мм) и добавляет коэффициенты для разных моделей.
  * Минимальная высота упаковки — 20 мм.
*   **Пример**:

    ```php
    try {
        $info = $informator->getPackagingInfo(201, 600, 400, 100, 10);
        print_r($info);
        // Вывод: ['result' => true, 'data' => ['length' => 1006, 'width' => 506, 'height' => 50]]
    } catch (PDOException $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Все методы, взаимодействующие с базой данных, могут выбросить `PDOException`. Оборачивайте вызовы в try-catch.

    ```php
    try {
        $cardboard = $informator->getCardboard();
        print_r($cardboard);
    } catch (PDOException $e) {
        error_log('Ошибка: ' . $e->getMessage());
        echo 'Произошла ошибка';
    }
    ```
* **Валидация конфигурации**: Убедитесь, что `$config` содержит корректные данные для всех трех баз данных, включая `host`, `database`, и `users.read`.
* **Режим разработки**: Используйте `$dev = true` для отображения скрытых данных (например, коробок с `onSale = false` или цветов `SNOW-WHITE`).
* **Схема базы данных**: Убедитесь, что таблицы (`calculator_promocodes`, `circulation`, `type_orders`, `cardboardStore`, `types_boxes`, `cardboardWorkArea`, `cells_0933`) существуют и содержат ожидаемые поля.
* **Логирование**: Внедрите логирование запросов и ошибок для упрощения отладки.
* **Кэширование**: Данные из методов `getPromocodes`, `getCirculation`, `getMarkups`, `getCardboard`, `getColors`, `getTypes` редко меняются. Рассмотрите кэширование результатов для оптимизации производительности.

***

### 🐛 Устранение неполадок

* **Ошибка PDO**: Если методы выбрасывают `PDOException`:
  * Проверьте корректность конфигурации баз данных (`host`, `username`, `password`, `database`).
  * Убедитесь, что сервер базы данных доступен и PDO настроен с `ERRMODE_EXCEPTION`.
* **Пустой результат**: Если методы возвращают пустой массив `data`:
  * Проверьте наличие записей в соответствующих таблицах.
  * Убедитесь, что в не-`dev` режиме данные не фильтруются (например, `onSale` для типов коробок или исключенные цвета).
* **Некорректные размеры**:
  * Для `findWorkSize`: Проверьте, существует ли лист с указанным цветом и размерами в `cardboardStore` и `cardboardWorkArea`.
  * Для `getPackagingInfo`: Убедитесь, что `$model` соответствует поддерживаемому типу коробки.
  * Для `getBoxSize933`: Проверьте, есть ли запись для `$cellNumber` в таблице `cells_0933`.
* **Неизвестный тип коробки**: Если `getSizeNonGenerative` или `getPackagingInfo` возвращают `text_error`:
  * Проверьте, что `$model` соответствует одному из поддерживаемых кодов (1301, 1302, 1303, 1701 для `getSizeNonGenerative`; 1006, 110, 201, 215, 330, 426, 427, 1301, 1302, 1303, 1701, 933 для `getPackagingInfo`).
* **Цвет не распознан**: Если `findWorkSize` возвращает исходные размеры, проверьте, что `$sheetColor` соответствует значению в `colorCardboard` или `colorCardboardEn` в `cardboardStore`.

***
