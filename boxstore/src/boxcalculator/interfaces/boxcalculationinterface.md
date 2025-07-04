---
icon: php
---

# BoxCalculationInterface

## 📦 Документация по интерфейсу BoxCalculationInterface

Добро пожаловать в документацию по интерфейсу `BoxCalculationInterface`! 🎉 Этот PHP-интерфейс, расположенный в пространстве имен `BoxCalculator\Interfaces`, определяет контракт для классов, которые выполняют калькуляцию параметров и стоимости коробок различных типов (FEFCO и негенеративных) в системе калькуляции коробок. Интерфейс включает методы для расчета характеристик коробок, таких как размеры, стоимость материалов, производство и доставка. Ниже вы найдете подробное описание методов интерфейса, их назначение и рекомендации по реализации.

***

### 🚀 Обзор

Интерфейс `BoxCalculationInterface` разработан для обеспечения единообразного подхода к калькуляции коробок, включая:

* Мульти-калькуляцию для выбора подходящего метода расчета на основе типа коробки 📋
* Расчет параметров и стоимости для коробок типов 427, 201, 215, 330, 1006, 426, 933 и негенеративных коробок (1301, 1302, 1701) 📦
* Учет производственных затрат, логистики, материалов и количества коробок 🛠️
* Поддержка двух режимов расчета: для производства и для клиентов ⚙️

Интерфейс предназначен для реализации в классах, таких как `BoxCalculation`, которые интегрируются с другими компонентами системы (например, `DeliveryCalculation`, `Other`) для выполнения комплексных расчетов.

***

### 🛠️ Установка и настройка

Чтобы использовать интерфейс `BoxCalculationInterface`, выполните следующие шаги:

1.  **Подключите интерфейс**: Убедитесь, что файл интерфейса включен в ваш PHP-проект.

    ```php
    require_once 'path/to/BoxCalculationInterface.php';
    ```
2.  **Создайте реализующий класс**: Реализуйте интерфейс в классе, например, `BoxCalculation`, который будет выполнять калькуляцию коробок.

    ```php
    namespace BoxCalculator;

    use BoxCalculator\Interfaces\BoxCalculationInterface;
    use PDO;

    class BoxCalculation implements BoxCalculationInterface {
        private $logistics;
        private $other;

        public function __construct(array $config) {
            // Инициализация PDO и зависимостей
        }

        // Реализация методов интерфейса (см. ниже)
    }
    ```
3.  **Настройте зависимости**: Убедитесь, что реализующий класс имеет доступ к PDO-подключениям и объектам, таким как `DeliveryCalculation` и `Other`.

    ```php
    $config = [/* конфигурация баз данных */];
    $boxCalculation = new BoxCalculation($config);
    ```
4. **Используйте методы**: Вызывайте методы реализующего класса для калькуляции коробок.

***

### 📋 Методы интерфейса

Интерфейс `BoxCalculationInterface` определяет девять методов, которые должны быть реализованы в классах. Ниже описаны их назначение, параметры, возвращаемые значения и рекомендации по реализации.

#### 1. `calculateBoxMulti(string $typeBox, int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false): array` 📋

Выполняет мульти-калькуляцию для коробки указанного типа, перенаправляя запрос на соответствующий метод.

* **Параметры**:
  * `$typeBox` (string): Тип коробки (427, 201, 215, 330, 1006, 426, 933, 1301, 1302, 1701).
  * `$lBox` (int): Длина коробки, мм.
  * `$wBox` (int): Ширина коробки, мм.
  * `$hBox` (int): Высота коробки, мм.
  * `$colorBox` (string): Цвет коробки (например, `white`, `ECO-BROWN`, `LIME-BARBIE`).
  * `$amountBox` (int): Количество коробок.
  * `$clientCalculationBox` (bool): Режим расчета (для клиентов — `true`, для производства — `false`, по умолчанию `false`).
* **Возвращает**: Массив с ключами:
  * `status` (bool): Успешность калькуляции (`true`/`false`).
  * `data` (array): Параметры коробки (при успехе).
  * `message` (string): Сообщение об ошибке (при неудаче).
* **Рекомендации по реализации**:
  * Используйте `switch` или аналогичную конструкцию для вызова соответствующего метода калькуляции на основе `$typeBox`.
  * Проверяйте, что `$typeBox` соответствует одному из поддерживаемых типов.
  * Передавайте параметры (`$lBox`, `$wBox`, `$hBox`, `$colorBox`, `$amountBox`, `$clientCalculationBox`) в целевой метод.
  * Для типа 933 добавьте параметр `$cells` (например, по умолчанию 4).
  * Обрабатывайте случаи, когда `$typeBox` неизвестен, возвращая ошибку.
*   **Пример использования**:

    ```php
    $result = $boxCalculation->calculateBoxMulti('427', 200, 150, 100, 'white', 10, false);
    print_r($result);
    ```

***

#### 2. `calculateBox427(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false): array` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0427.

* **Параметры**:
  * `$lBox` (int): Длина коробки, мм.
  * `$wBox` (int): Ширина коробки, мм.
  * `$hBox` (int): Высота коробки, мм.
  * `$colorBox` (string): Цвет коробки.
  * `$amountBox` (int): Количество коробок.
  * `$clientCalculationBox` (bool): Режим расчета (по умолчанию `false`).
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Рекомендации по реализации**:
  * Получите данные о картоне через `Other::getCardboardForBox` с учетом `$colorBox` и `$clientCalculationBox`.
  * Рассчитайте размеры развертки (`WP`, `LP`) с учетом толщины картона и зазоров.
  * Учитывайте возможность оптимизации раскладки (например, "перевернутая" раскладка).
  * Вычислите себестоимость, включая материалы, резку, амортизацию и электроэнергию.
  * Интегрируйте логистику через `DeliveryCalculation::calculateLogistics427`.
  * Выберите вариант с минимальной ценой (`priceBoxstore`).
  * Возвращайте ошибку, если подходящий картон не найден.
*   **Пример использования**:

    ```php
    $result = $boxCalculation->calculateBox427(200, 150, 100, 'white', 10);
    print_r($result);
    ```

***

#### 3. `calculateBox201(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false): array` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0201.

* **Параметры**: Аналогичны `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Рекомендации по реализации**:
  * Аналогично `calculateBox427`, но с учетом специфики FEFCO 0201.
  * Учитывайте дополнительные затраты на склейку (например, стоимость ленты и работы).
  * Используйте `DeliveryCalculation::calculateLogistics201` для логистики.
*   **Пример использования**:

    ```php
    $result = $boxCalculation->calculateBox201(200, 150, 100, 'white', 10);
    print_r($result);
    ```

***

#### 4. `calculateBox215(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false): array` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0215.

* **Параметры**: Аналогичны `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Рекомендации по реализации**:
  * Аналогично `calculateBox427`, с учетом уникальных размеров развертки FEFCO 0215.
  * Учитывайте затраты на склейку.
  * Используйте `DeliveryCalculation::calculateLogistics215` для логистики.
*   **Пример использования**:

    ```php
    $result = $boxCalculation->calculateBox215(200, 150, 100, 'white', 10);
    print_r($result);
    ```

***

#### 5. `calculateBox330(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false): array` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0330.

* **Параметры**: Аналогичны `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Рекомендации по реализации**:
  * Аналогично `calculateBox427`, с учетом специфики FEFCO 0330.
  * Корректируйте время резки в зависимости от длины реза (`lineCutOne`).
  * Используйте `DeliveryCalculation::calculateLogistics330` для логистики.
*   **Пример использования**:

    ```php
    $result = $boxCalculation->calculateBox330(200, 150, 100, 'white', 10);
    print_r($result);
    ```

***

#### 6. `calculateBox1006(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false): array` 📦

Рассчитывает параметры и стоимость шестигранной коробки (1006).

* **Параметры**: Аналогичны `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Рекомендации по реализации**:
  * Аналогично `calculateBox427`, с учетом размеров развертки шестигранной коробки.
  * Корректируйте время резки в зависимости от длины реза.
  * Используйте `DeliveryCalculation::calculateLogistics1006` для логистики.
*   **Пример использования**:

    ```php
    $result = $boxCalculation->calculateBox1006(200, 200, 100, 'white', 10);
    print_r($result);
    ```

***

#### 7. `calculateBoxNonGenerative(string $typeBox, int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false): array` 📦

Рассчитывает параметры и стоимость негенеративных коробок (1301, 1302, 1701).

* **Параметры**:
  * `$typeBox` (string): Тип коробки (1301, 1302, 1701).
  * Остальные параметры аналогичны `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Рекомендации по реализации**:
  * Используйте фиксированные размеры и параметры для каждой модели (например, 380x280x310 мм для 1301).
  * Учитывайте дополнительные затраты (например, шнурки для 1701).
  * Поддерживайте расчет для нескольких листов картона (для 1701).
  * Используйте `DeliveryCalculation::calculateLogisticsNonGenerative` для логистики.
*   **Пример использования**:

    ```php
    $result = $boxCalculation->calculateBoxNonGenerative('1301', 375, 278, 308, 'white', 10);
    print_r($result);
    ```

***

#### 8. `calculateBox933(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false, $cells = 4): array` 📦

Рассчитывает параметры и стоимость коробки типа 933 (ложемент).

* **Параметры**:
  * `$cells` (int): Количество ячеек (по умолчанию 4).
  * Остальные параметры аналогичны `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Рекомендации по реализации**:
  * Получите данные о ячейках через `Other::getCellList933`.
  * Рассчитайте размеры развертки (`WP`, `LP`) с учетом количества ячеек.
  * Примените коэффициенты по тиражу и времени резки.
  * Используйте `DeliveryCalculation::calculateLogistics933` для логистики.
  * Убедитесь, что `$cells >= 4`.
*   **Пример использования**:

    ```php
    $result = $boxCalculation->calculateBox933(50, 40, 100, 'white', 10, false, 4);
    print_r($result);
    ```

***

#### 9. `calculateBox426(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false): array` 📦

Рассчитывает параметры и стоимость коробки типа FEFCO 0426.

* **Параметры**: Аналогичны `calculateBox427`.
* **Возвращает**: Массив с ключами `status`, `data` (или `message`).
* **Рекомендации по реализации**:
  * Аналогично `calculateBox427`, с учетом уникальных размеров развертки FEFCO 0426.
  * Используйте `DeliveryCalculation::calculateLogistics426` для логистики.
*   **Пример использования**:

    ```php
    $result = $boxCalculation->calculateBox426(200, 150, 100, 'white', 10);
    print_r($result);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Реализуйте надежную обработку исключений, особенно для операций с базой данных и логистикой.

    ```php
    public function calculateBox427(int $lBox, int $wBox, int $hBox, string $colorBox, int $amountBox, bool $clientCalculationBox = false): array {
        try {
            // Логика калькуляции
        } catch (Exception $e) {
            return ['status' => false, 'message' => 'Ошибка: ' . $e->getMessage()];
        }
    }
    ```
* **Валидация параметров**:
  * Проверяйте, что `$typeBox` соответствует поддерживаемым типам (427, 201, 215, 330, 1006, 426, 933, 1301, 1302, 1701).
  * Убедитесь, что `$lBox`, `$wBox`, `$hBox`, `$amountBox`, `$cells` — положительные числа.
  * Проверьте, что `$colorBox` соответствует доступным цветам из `Other::getCardboardForBox`.
* **Режим расчета**:
  * Используйте `$clientCalculationBox = true` для клиентских расчетов, чтобы исключить внутренние оптимизации (например, перевернутую раскладку).
  * Для производственного режима (`$clientCalculationBox = false`) включайте все оптимизации.
* **Интеграция с логистикой**:
  * Убедитесь, что `DeliveryCalculation` настроен корректно и поддерживает методы `calculateLogistics*`.
  * Проверяйте, что параметры логистики (например, `$totalVolWeightPostSDEK_MUUL`, `$priceMuulResult`) корректны.
* **Кэширование**:
  * Кэшируйте данные о картоне и параметрах калькуляции, если они редко меняются.
  * Рассмотрите кэширование результатов для часто повторяющихся запросов.
* **Логирование**: Внедрите логирование входных параметров и результатов для анализа затрат и оптимизации.
* **Оптимизация**:
  * Минимизируйте запросы к базе данных, используя кэширование или пакетные запросы.
  * Проверяйте влияние коэффициентов (`ozonCoef`, `muulCoef`, `KPM`) на итоговую цену, особенно для больших тиражей.

***

### 🐛 Устранение неполадок

* **Ошибка PDO**:
  * Проверьте конфигурацию PDO-подключений в конструкторе класса.
  * Убедитесь, что таблицы (`cardboardStore`, `calculator_params`, `cells_0933`) существуют и содержат данные.
* **Не найден подходящий картон**:
  * Проверьте, возвращает ли `Other::getCardboardForBox` данные для `$colorBox`.
  * Убедитесь, что `$clientCalculationBox` соответствует требуемому режиму.
* **Нулевая стоимость**:
  * Проверьте данные о картоне (`priceSheet`, `wSheetWork`, `lSheetWork`) на корректность.
  * Убедитесь, что размеры развертки (`WP`, `LP`) рассчитаны правильно.
* **Ошибки логистики**:
  * Проверьте, что методы `DeliveryCalculation::calculateLogistics*` возвращают корректные данные.
  * Убедитесь, что `$totalVolWeightPostSDEK_MUUL` и `$priceMuulResult` установлены корректно.
* **Некорректные размеры для 933**:
  * Проверьте, что `$cells >= 4` и данные о ячейках (`Other::getCellList933`) корректны.
* **Ошибки негенеративных коробок**:
  * Убедитесь, что `$typeBox` — один из 1301, 1302, 1701.
  * Проверьте, что фиксированные размеры коробок актуальны.

***
