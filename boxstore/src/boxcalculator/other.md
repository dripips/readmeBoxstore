---
icon: php
---

# Other

## 📋 Документация по классу Other

Добро пожаловать в документацию по классу `Other`! 🎉 Этот PHP-класс, расположенный в пространстве имен `BoxCalculator`, реализует интерфейс `OtherInterface` и предназначен для взаимодействия с базами данных (`cardboard_store`, `cloud_muul`, `muul_formulas_drawings`) через PDO. Класс предоставляет методы для получения данных о картоне, параметрах калькуляции, коэффициентах маржинальности, данных для ложементов и информации по артикулам. Ниже вы найдете подробное руководство по использованию, описание методов и рекомендации по внедрению.

***

### 🚀 Обзор

Класс `Other` разработан для выполнения следующих задач:

* Получение данных о подходящем картоне для производства коробок с учетом цвета и режима расчета (для клиентов или производства) 📦
* Извлечение параметров калькуляции из базы данных 📋
* Расчет коэффициента маржинальности и множителя для ложементов 🔢
* Получение данных о ячейках для коробок типа 933 (ложементы) 🧩
* Извлечение информации о цветах картона и разбор артикулов для калькуляции 🎨

Класс использует PDO для безопасного взаимодействия с тремя базами данных, инициализированными в конструкторе, и предоставляет методы, необходимые для интеграции с классом `BoxCalculation`. Он обрабатывает ошибки через исключения и поддерживает два режима расчета (клиентский и производственный).

***

### 🛠️ Установка и настройка

Чтобы использовать класс `Other`, выполните следующие шаги:

1.  **Подключите класс и интерфейс**: Убедитесь, что файлы класса `Other` и интерфейса `OtherInterface` включены в ваш PHP-проект.

    ```php
    require_once 'path/to/Other.php';
    require_once 'path/to/OtherInterface.php';
    ```
2.  **Создайте интерфейс OtherInterface**: Убедитесь, что интерфейс существует и определяет необходимые методы. Пример:

    ```php
    namespace BoxCalculator\Interfaces;

    interface OtherInterface {
        public function getCardboardForBox(string $color, bool $clientCalculationBox = false): array;
        public function getParamsCalculated(): array;
        public function getMarginValue(int $amountBox, bool $itLozhement = false): int;
        public function getCountMultiply(int $amountBox): int;
        public function getCellList933($cells);
        public function getColorsById(int $cardboardId, string $mode = 'full');
        public function getDataForCalculation(string $article): array;
    }
    ```
3.  **Инициализируйте PDO-подключения**: Создайте объекты PDO для баз данных `cardboard_store`, `cloud_muul` и `muul_formulas_drawings`.

    ```php
    $pdoCardboardStore = new PDO("mysql:host=localhost;dbname=cardboard_store", "user", "pass");
    $pdoCloudMuul = new PDO("mysql:host=localhost;dbname=cloud_muul", "user", "pass");
    $pdoDrawings = new PDO("mysql:host=localhost;dbname=muul_formulas", "user", "pass");
    ```
4.  **Создайте экземпляр класса**: Передайте PDO-объекты в конструктор.

    ```php
    use BoxCalculator\Other;
    try {
        $other = new Other($pdoCardboardStore, $pdoCloudMuul, $pdoDrawings);
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```
5. **Используйте методы**: Вызывайте методы для получения данных или расчетов.

***

### 📋 Методы и использование

Класс `Other` предоставляет семь методов для работы с данными и расчетами. Ниже описаны все методы.

#### 1. `__construct(PDO $pdoCardboardStore, PDO $pdoCloudMuul, PDO $pdoDrawings)` 🛠️

Инициализирует класс, сохраняя PDO-объекты для баз данных.

* **Параметры**:
  * `$pdoCardboardStore` (PDO): Подключение к базе `cardboard_store`.
  * `$pdoCloudMuul` (PDO): Подключение к базе `cloud_muul`.
  * `$pdoDrawings` (PDO): Подключение к базе `muul_formulas_drawings`.
* **Возвращает**: Ничего
*   **Пример**:

    ```php
    $other = new Other($pdoCardboardStore, $pdoCloudMuul, $pdoDrawings);
    ```

***

#### 2. `getCardboardForBox(string $color, bool $clientCalculationBox = false)` 📦

Получает список подходящего картона для производства коробок по цвету.

* **Параметры**:
  * `$color` (string): Цвет картона (на русском или английском, например, `white`, `бурый`, `LIME-BARBIE`).
  * `$clientCalculationBox` (bool): Режим расчета (для клиентов — `true`, для производства — `false`, по умолчанию `false`).
* **Возвращает**: Массив с данными о подходящем картоне (цена, размеры, толщина, цвет, ID и т.д.).
* **Исключения**: `Exception` при ошибке выполнения запроса.
* **Логика**:
  * Запрашивает рабочие области из таблицы `cardboardWorkArea`.
  * Извлекает данные о картоне из таблицы `cardboardStore`, фильтруя по цвету и `useCalculated = 1`.
  * Получает `id1C` из таблицы `cardboardStore1C`.
  * В клиентском режиме (`$clientCalculationBox = true`) использует `priceSheetCustomer` и фильтрует по `customerStatus`.
  * В производственном режиме использует `priceSheetProduction` и фильтрует по `productionStatus`.
  * Добавляет размеры рабочих областей (`lSheetWork`, `wSheetWork`), если они есть в `cardboardWorkArea`.
*   **Пример**:

    ```php
    try {
        $cardboards = $other->getCardboardForBox('white', false);
        print_r($cardboards);
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 3. `getParamsCalculated()` 📋

Получает основные параметры для калькуляции из базы данных.

* **Параметры**: Нет
* **Возвращает**: Массив с параметрами (имя => значение) и списком KPM (коэффициентов полезного материала).
* **Исключения**: `Exception` при ошибке выполнения запроса.
* **Логика**:
  * Запрашивает параметры из таблицы `calculator_params` в базе `cloud_muul`.
  * Извлекает данные из таблицы `KPM` для коэффициентов полезного материала.
  * Формирует ассоциативный массив с параметрами и добавляет KPM как подмассив.
*   **Пример**:

    ```php
    try {
        $params = $other->getParamsCalculated();
        print_r($params);
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 4. `getMarginValue(int $amountBox, bool $itLozhement = false)` 🔢

Получает коэффициент маржинальности на основе количества коробок.

* **Параметры**:
  * `$amountBox` (int): Количество коробок.
  * `$itLozhement` (bool): Использовать таблицу для ложементов (`marginLozhement`) или стандартную (`margin`) (по умолчанию `false`).
* **Возвращает**: `int` — значение коэффициента маржинальности.
* **Исключения**: `Exception` при ошибке выполнения запроса.
* **Логика**:
  * Запрашивает данные из таблицы `margin` или `marginLozhement` в базе `cloud_muul`.
  * Выбирает максимальный `countBox`, который меньше или равен `$amountBox`, и возвращает соответствующий `margin`.
*   **Пример**:

    ```php
    try {
        $margin = $other->getMarginValue(100);
        echo $margin;
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 5. `getCountMultiply(int $amountBox)` 🔢

Получает множитель для коэффициента добавочного по количеству для коробок типа 933.

* **Параметры**:
  * `$amountBox` (int): Количество коробок.
* **Возвращает**: `int` — значение множителя.
* **Исключения**: `Exception` при ошибке выполнения запроса.
* **Логика**:
  * Запрашивает значения `countBox` из таблицы `margin` в базе `cloud_muul`.
  * Сортирует значения по убыванию.
  * Подсчитывает количество значений `countBox`, которые больше `$amountBox` и меньше 1500.
*   **Пример**:

    ```php
    try {
        $multiplier = $other->getCountMultiply(100);
        echo $multiplier;
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 6. `getCellList933($cells)` 🧩

Получает данные о ячейках для коробок типа 933 (ложементов).

* **Параметры**:
  * `$cells` (int): Количество ячеек.
* **Возвращает**: Массив с данными о ячейках (`cell_number_l`, `cell_number_w`) или пустой массив при ошибке.
* **Логика**:
  * Запрашивает данные из таблицы `cells_0933` в базе `muul_formulas_drawings` по количеству ячеек (`amount = $cells`).
  * Возвращает первый результат.
*   **Пример**:

    ```php
    $cellData = $other->getCellList933(4);
    print_r($cellData); // Вывод: ['cell_number_l' => ..., 'cell_number_w' => ...]
    ```

***

#### 7. `getColorsById(int $cardboardId, string $mode = 'full')` 🎨

Получает информацию о цвете картона по его ID.

* **Параметры**:
  * `$cardboardId` (int): ID картона из таблицы `cardboardColors`.
  * `$mode` (string): Режим вывода (`full` — все данные, `en` — английское название, `ru` — русское название, по умолчанию `full`).
* **Возвращает**:
  * Для `full`: Массив с ключами `success` и `data` (или `message` при ошибке).
  * Для `en`/`ru`: Строка с названием цвета или `'-'` при отсутствии.
* **Исключения**: `PDOException` при ошибке выполнения запроса.
* **Логика**:
  * Запрашивает данные из таблицы `cardboardColors` по `cardboardID`.
  * Возвращает данные в зависимости от режима:
    * `full`: весь результат запроса.
    * `en`: поле `cardboardNameEn`.
    * `ru`: поле `cardboardNameRu`.
  * При отсутствии данных возвращает сообщение об ошибке.
*   **Пример**:

    ```php
    try {
        $color = $other->getColorsById(1, 'en');
        echo $color; // Вывод: 'white'
        $fullData = $other->getColorsById(1);
        print_r($fullData);
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 8. `getDataForCalculation(string $article)` 📋

Разбирает артикул товара и возвращает данные для калькуляции.

* **Параметры**:
  * `$article` (string): Артикул в формате `TYPE-CARDBOARD-COLOR-LENGTH-WIDTH-HEIGHT-QUANTITY-GROUP` или с цветом как список ID.
* **Возвращает**: Массив с ключами `success` и `data` (список разобранных данных).
* **Логика**:
  * Использует регулярное выражение для разбора артикула.
  * Если цвет содержит дефисы и состоит из букв (не ID), возвращает данные как есть.
  * Если цвет — это список ID (например, `1-2`), запрашивает цвета по ID через `getColorsById` и формирует подтипы (`TYPE_1`, `TYPE_2` и т.д.).
*   **Пример**:

    ```php
    $data = $other->getDataForCalculation('0427-CARDBOARD-WHITE-200-150-100-10-XXX');
    print_r($data);
    $data = $other->getDataForCalculation('0427-CARDBOARD-1-2-200-150-100-10-XXX');
    print_r($data);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Все методы выбрасывают исключения при ошибках базы данных. Оборачивайте вызовы в try-catch.

    ```php
    try {
        $cardboards = $other->getCardboardForBox('white');
        print_r($cardboards);
    } catch (Exception $e) {
        error_log('Ошибка: ' . $e->getMessage());
        echo 'Произошла ошибка';
    }
    ```
* **Валидация параметров**:
  * Проверяйте, что `$color` соответствует значениям в `colorCardboard` или `colorCardboardEn`.
  * Убедитесь, что `$amountBox` и `$cells` — положительные числа.
  * Для `getColorsById` проверяйте, что `$cardboardId` существует в `cardboardColors`.
  * Для `getDataForCalculation` убедитесь, что артикул соответствует формату.
* **Режим расчета**: Используйте `$clientCalculationBox = true` для клиентских расчетов, чтобы учитывать только картон с `customerStatus = 1`.
* **Кэширование**:
  * Данные из `getParamsCalculated` и `getCardboardForBox` можно кэшировать, если они редко меняются.
  * Кэшируйте результаты `getCellList933` для часто используемых значений `$cells`.
* **Логирование**: Внедрите логирование запросов и ошибок для анализа проблем с базой данных.
* **Схема базы данных**:
  * Убедитесь, что таблицы `cardboardStore`, `cardboardWorkArea`, `cardboardStore1C`, `cardboardColors`, `calculator_params`, `KPM`, `margin`, `marginLozhement`, `cells_0933` существуют и содержат ожидаемые поля.
  * Проверьте, что поле `useCalculated` в `cardboardStore` корректно фильтрует данные.

***

### 🐛 Устранение неполадок

* **Ошибка PDO**: Если методы выбрасывают `Exception`:
  * Проверьте, что PDO-объекты инициализированы корректно и имеют доступ к базам данных.
  * Убедитесь, что таблицы и поля существуют (например, `cardboardStore.useCalculated`, `cardboardColors.cardboardID`).
* **Пустой результат**:
  * Для `getCardboardForBox`: Проверьте, что `$color` соответствует значениям в `colorCardboard` или `colorCardboardEn`, и что есть записи с `useCalculated = 1` и соответствующим статусом (`customerStatus` или `productionStatus`).
  * Для `getParamsCalculated`: Убедитесь, что таблицы `calculator_params` и `KPM` содержат данные.
  * Для `getCellList933`: Проверьте, что в `cells_0933` есть запись для указанного `$cells`.
* **Некорректный коэффициент маржинальности**:
  * Проверьте, что таблица `margin` или `marginLozhement` содержит записи с подходящими значениями `countBox`.
  * Убедитесь, что `$amountBox` — положительное число.
* **Ошибки разбора артикула**:
  * Проверьте, что `$article` соответствует формату `TYPE-CARDBOARD-COLOR-LENGTH-WIDTH-HEIGHT-QUANTITY-GROUP`.
  * Для цветов в формате ID (например, `1-2`) убедитесь, что ID существуют в `cardboardColors`.
* **Отсутствие id1C**:
  * Проверьте, что таблица `cardboardStore1C` содержит записи для всех `cardboardID` из `cardboardStore`.

***
