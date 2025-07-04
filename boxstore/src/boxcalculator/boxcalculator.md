---
icon: php
---

# BoxCalculator

## 📏 Документация по классу BoxCalculation

Добро пожаловать в документацию по классу `BoxCalculation`! 🎉 Этот PHP-класс, расположенный в пространстве имен `BoxCalculator`, предназначен для калькуляции параметров и стоимости производства коробок различных типов (FEFCO и негенеративных) с учетом логистики, материалов и производственных характеристик. Класс использует PDO для взаимодействия с базами данных, интегрируется с объектами `DeliveryCalculation` и `Other`, и предоставляет методы для расчета стоимости, размеров и доставки. Ниже вы найдете подробное руководство по использованию, описание методов и рекомендации по внедрению.

***

### 🚀 Обзор

Класс `BoxCalculation` разработан для выполнения следующих задач:

* Калькуляция стоимости и параметров коробок различных типов (427, 201, 215, 330, 1006, 426, 933, 1301, 1302, 1701) 📦
* Учет производственных затрат, включая материалы, резку, склейку и амортизацию 🛠️
* Интеграция с логистикой для расчета стоимости упаковки и доставки 🚚
* Поддержка режимов расчета для производства (`clientCalculationBox = false`) и клиентов (`clientCalculationBox = true`) ⚙️
* Получение данных для калькуляции по артикулу 📋

Класс инициализирует PDO-подключения к трем базам данных (`cardboard_store`, `cloud_muul`, `muul_formulas_drawings`), использует объект `Other` для получения данных о картоне и параметрах, и объект `DeliveryCalculation` для расчета логистики. Он оптимизирован для выбора наиболее экономичного варианта картона и предоставляет детализированные результаты, включая цены для Boxstore, Ozon и MUUL.

***

### 🛠️ Установка и настройка

Чтобы использовать класс `BoxCalculation`, выполните следующие шаги:

1.  **Подключите класс и зависимости**: Убедитесь, что файлы класса `BoxCalculation`, а также классы `DeliveryCalculation` и `Other` включены в ваш PHP-проект.

    ```php
    require_once 'path/to/BoxCalculation.php';
    require_once 'path/to/DeliveryCalculation.php';
    require_once 'path/to/Other.php';
    ```
2. **Настройте конфигурацию**:
   * Подготовьте массив конфигурации с данными для подключения к базам данных (`cardboard_store`, `cloud_muul`, `muul_formulas_drawings`).
   * Каждая база данных должна содержать ключи `host`, `database`, и `users.read` с `username` и `password`.
3.  **Создайте экземпляр класса**: Передайте конфигурацию в конструктор.

    ```php
    use BoxCalculator\BoxCalculation;
    $config = [
        'databases' => [
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
    try {
        $boxCalculation = new BoxCalculation($config);
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```
4. **Используйте методы**: Вызывайте методы для калькуляции коробок или получения данных по артикулу.

***

### 📋 Методы и использование

Класс `BoxCalculation` предоставляет методы для калькуляции коробок различных типов, а также метод для получения данных по артикулу. Ниже описаны все методы.

#### 1. `__construct(array $config)` 🛠️

Инициализирует класс, устанавливая PDO-подключения к базам данных и объекты `DeliveryCalculation` и `Other`.

* **Параметры**:
  * `$config` (array): Конфигурация с данными для баз данных (`cardboard_store`, `cloud_muul`, `muul_formulas_drawings`).
* **Возвращает**: Ничего
* **Исключения**: `PDOException` при ошибке подключения к базам данных.
*   **Пример**:

    ```php
    try {
        $boxCalculation = new BoxCalculation($config);
        echo 'Инициализация успешна';
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 2. `calculateBoxMulti(string $typeBox, int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false, int $cells = 0)` 📦

Выполняет мульти-калькуляцию для коробки указанного типа, перенаправляя запрос на соответствующий метод.

* **Параметры**:
  * `$typeBox` (string): Тип коробки (427, 201, 215, 330, 1006, 426, 933, 1301, 1302, 1701).
  * `$lBox` (int): Длина коробки, мм.
  * `$wBox` (int): Ширина коробки, мм.
  * `$hBox` (int): Высота коробки, мм.
  * `$colorBox` (string): Цвет коробки (например, `white`, `ECO-BROWN`, `LIME-BARBIE`).
  * `$amountBox` (int): Количество коробок.
  * `$clientCalculationBox` (bool): Режим расчета (для клиентов — `true`, для производства — `false`, по умолчанию `false`).
  * `$cells` (int): Количество ячеек (для типа 933, по умолчанию 0).
* **Возвращает**: Массив с ключами:
  * `status` (bool): Успешность калькуляции (`true`/`false`).
  * `data` (array): Параметры коробки (при успехе).
  * `message` (string): Сообщение об ошибке (при неудаче).
*   **Пример**:

    ```php
    try {
        $result = $boxCalculation->calculateBoxMulti('427', 200, 150, 100, 'white', 10, false);
        if ($result['status']) {
            print_r($result['data']);
        } else {
            echo 'Ошибка: ' . $result['message'];
        }
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

#### 3. `calculateBox427(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false)` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0427.

* **Параметры**: Те же, что в `calculateBoxMulti`, за исключением `$typeBox` и `$cells`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Логика**:
  * Выбирает подходящий картон через `$this->other->getCardboardForBox`.
  * Рассчитывает размеры развертки (`WP`, `LP`) с учетом толщины и зазоров.
  * Учитывает возможность "перевернутой" раскладки (reverse) для оптимизации.
  * Рассчитывает себестоимость, включая материалы, резку, склейку, амортизацию и электроэнергию.
  * Добавляет стоимость логистики через `$this->logistics->calculateLogistics427`.
  * Выбирает вариант с минимальной ценой (`priceBoxstore`).
*   **Пример**:

    ```php
    $result = $boxCalculation->calculateBox427(200, 150, 100, 'white', 10);
    print_r($result['data']);
    ```

***

#### 4. `calculateBox201(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false)` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0201.

* **Параметры**: Те же, что в `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Логика**: Аналогична `calculateBox427`, но с учетом специфики FEFCO 0201, включая затраты на склейку.
*   **Пример**:

    ```php
    $result = $boxCalculation->calculateBox201(200, 150, 100, 'white', 10);
    print_r($result['data']);
    ```

***

#### 5. `calculateBox215(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false)` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0215.

* **Параметры**: Те же, что в `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Логика**: Аналогична `calculateBox427`, с учетом уникальных размеров развертки и затрат на склейку.
*   **Пример**:

    ```php
    $result = $boxCalculation->calculateBox215(200, 150, 100, 'white', 10);
    print_r($result['data']);
    ```

***

#### 6. `calculateBox330(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false)` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0330.

* **Параметры**: Те же, что в `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Логика**:
  * Учитывает корректировку времени резки в зависимости от длины реза (`lineCutOne`).
  * Использует логистику через `$this->logistics->calculateLogistics330`.
*   **Пример**:

    ```php
    $result = $boxCalculation->calculateBox330(200, 150, 100, 'white', 10);
    print_r($result['data']);
    ```

***

#### 7. `calculateBox1006(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false)` 📦

Рассчитывает параметры и стоимость шестигранной коробки (1006).

* **Параметры**: Те же, что в `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Логика**:
  * Учитывает корректировку времени резки в зависимости от длины реза.
  * Использует логистику через `$this->logistics->calculateLogistics1006`.
*   **Пример**:

    ```php
    $result = $boxCalculation->calculateBox1006(200, 200, 100, 'white', 10);
    print_r($result['data']);
    ```

***

#### 8. `calculateBox933(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false, int $cells = 4)` 📦

Рассчитывает параметры и стоимость коробки типа 933 (ложемент).

* **Параметры**:
  * Те же, что в `calculateBox427`, плюс `$cells` (количество ячеек, минимум 4).
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Логика**:
  * Получает данные о ячейках через `$this->other->getCellList933`.
  * Рассчитывает размеры развертки (`WP`, `LP`) с учетом ячеек.
  * Применяет коэффициенты по тиражу и времени резки.
  * Использует логистику через `$this->logistics->calculateLogistics933`.
*   **Пример**:

    ```php
    $result = $boxCalculation->calculateBox933(50, 40, 100, 'white', 10, false, 4);
    print_r($result['data']);
    ```

***

#### 9. `calculateBox426(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false)` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0426.

* **Параметры**: Те же, что в `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Логика**: Аналогична `calculateBox427`, с учетом уникальных размеров развертки.
*   **Пример**:

    ```php
    $result = $boxCalculation->calculateBox426(200, 150, 100, 'white', 10);
    print_r($result['data']);
    ```

***

#### 10. `calculateBoxNonGenerative(string $typeBox, int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false)` 📦

Рассчитывает параметры и стоимость негенеративных коробок (1301, 1302, 1701).

* **Параметры**: Те же, что в `calculateBox427`, плюс `$typeBox` (1301, 1302, 1701).
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Логика**:
  * Использует фиксированные размеры и параметры для каждой модели.
  * Учитывает дополнительные затраты (например, шнурки для 1701).
  * Поддерживает расчет для нескольких листов картона (для 1701).
*   **Пример**:

    ```php
    $result = $boxCalculation->calculateBoxNonGenerative('1301', 375, 278, 308, 'white', 10);
    print_r($result['data']);
    ```

***

#### 11. `getDataForCalculation(string $article)` 📋

Получает данные для калькуляции по артикулу.

* **Параметры**:
  * `$article` (string): Артикул коробки.
* **Возвращает**: Результат вызова `$this->other->getDataForCalculation`.
*   **Пример**:

    ```php
    $data = $boxCalculation->getDataForCalculation('0427-CARDBOARD-WHITE-200-150-100-10-XXX');
    print_r($data);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Все методы могут выбрасывать исключения от PDO или логистики. Оборачивайте вызовы в try-catch.

    ```php
    try {
        $result = $boxCalculation->calculateBox427(200, 150, 100, 'white', 10);
        print_r($result);
    } catch (Exception $e) {
        error_log('Ошибка: ' . $e->getMessage());
        echo 'Произошла ошибка';
    }
    ```
* **Валидация параметров**:
  * Проверяйте, что `$typeBox` соответствует поддерживаемым типам (427, 201, 215, 330, 1006, 426, 933, 1301, 1302, 1701).
  * Убедитесь, что `$lBox`, `$wBox`, `$hBox`, `$amountBox` — положительные числа.
  * Проверьте, что `$colorBox` соответствует доступным цветам из `$this->other->getCardboardForBox`.
* **Режим расчета**: Используйте `$clientCalculationBox = true` для клиентских расчетов, чтобы исключить внутренние оптимизации (например, reverse для 427).
* **Логистика**: Убедитесь, что объект `DeliveryCalculation` настроен корректно и поддерживает методы `calculateLogistics*` для каждого типа коробки.
* **Кэширование данных**: Методы `$this->other->getCardboardForBox` и `$this->other->getParamsCalculated` можно кэшировать, если данные редко меняются.
* **Логирование**: Внедрите логирование результатов калькуляции и ошибок для анализа производственных затрат и оптимизации.
* **Оптимизация**:
  * Для больших тиражей (`$amountBox`) проверьте влияние коэффициентов (`ozonCoef`, `muulCoef`, `KPM`) на итоговую цену.
  * Рассмотрите добавление проверки совместимости размеров развертки с листами картона перед расчетом.

***

### 🐛 Устранение неполадок

* **Ошибка PDO**: Если возникают исключения при подключении:
  * Проверьте конфигурацию баз данных (`$config['databases']`).
  * Убедитесь, что сервер MySQL доступен и пользователи имеют права на чтение.
* **Не найден подходящий картон**:
  * Проверьте, возвращает ли `$this->other->getCardboardForBox` подходящие листы для `$colorBox`.
  * Убедитесь, что `$clientCalculationBox` соответствует требуемому режиму (некоторые цвета могут быть недоступны для клиентов).
* **Нулевая стоимость (`costMatterOne <= 0`)**:
  * Проверьте данные о картоне (`priceSheet`, `wSheetWork`, `lSheetWork`) на корректность.
  * Убедитесь, что размеры развертки (`WP`, `LP`) рассчитаны правильно.
* **Ошибки логистики**:
  * Проверьте, что методы `calculateLogistics*` в `DeliveryCalculation` возвращают корректные данные.
  * Убедитесь, что `$this->totalVolWeightPostSDEK_MUUL` и `$this->priceMuulResult` установлены корректно.
* **Некорректные размеры для 933**:
  * Проверьте, что `$cells >= 4` и данные о ячейках (`$this->other->getCellList933`) корректны.
* **Ошибки негенеративных коробок**:
  * Убедитесь, что `$typeBox` соответствует 1301, 1302 или 1701.
  * Проверьте выбор картона для монохромных и цветных вариантов.

***
