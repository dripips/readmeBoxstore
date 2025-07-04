---
icon: php
---

# DeliveryCalculation

## 🚚 Документация по классу DeliveryCalculation

Добро пожаловать в документацию по классу `DeliveryCalculation`! 🎉 Этот PHP-класс, расположенный в пространстве имен `BoxCalculator`, реализует интерфейс `DeliveryCalculationInterface` и предназначен для расчета логистических параметров и стоимости доставки коробок различных типов (FEFCO и негенеративных). Класс предоставляет методы для вычисления стоимости доставки через Ozon и СДЭК, а также параметров упаковки, таких как габариты, вес и объемный вес. Ниже вы найдете подробное руководство по использованию, описание методов и рекомендации по внедрению.

***

### 🚀 Обзор

Класс `DeliveryCalculation` разработан для выполнения следующих задач:

* Расчет стоимости доставки через Ozon на основе цены коробки и количества 📦
* Вычисление логистических параметров для коробок типов 427, 201, 215, 330, 1006, 933 и негенеративных коробок (1301, 1302, 1701) 🚚
* Учет размеров развертки, толщины картона, количества коробок и параметров упаковки 📏
* Поддержка двух режимов расчета доставки через СДЭК (склад-склад и склад-дверь) с учетом НДС 🔧
* Интеграция с данными калькуляции коробок для точного учета стоимости и веса 📋

Класс реализует интерфейс `DeliveryCalculationInterface`, обеспечивая согласованность методов, и используется в связке с классом `BoxCalculation` для расчета итоговой стоимости заказов, включая упаковку и доставку.

***

### 🛠️ Установка и настройка

Чтобы использовать класс `DeliveryCalculation`, выполните следующие шаги:

1.  **Подключите класс и интерфейс**: Убедитесь, что файлы класса `DeliveryCalculation` и интерфейса `DeliveryCalculationInterface` включены в ваш PHP-проект.

    ```php
    require_once 'path/to/DeliveryCalculation.php';
    require_once 'path/to/DeliveryCalculationInterface.php';
    ```
2.  **Создайте интерфейс DeliveryCalculationInterface**: Убедитесь, что интерфейс существует и определяет необходимые методы. Пример:

    ```php
    namespace BoxCalculator\Interfaces;

    interface DeliveryCalculationInterface {
        public function getDeliveryOzon($ozonPrice, $amountBox): float;
        public function calculateLogistics427($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode): array;
        public function calculateLogistics201($lBox, $wBox, $hBox, $WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode): array;
        public function calculateLogistics215($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode, $lBox, $wBox, $hBox): array;
        public function calculateLogistics330($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode, $lBox, $wBox, $hBox): array;
        public function calculateLogistics1006($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode, $lBox, $wBox, $hBox): array;
        public function calculateLogistics933($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode, $lBox, $wBox, $hBox): array;
        public function calculateLogisticsNonGenerative($typeBox, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode): array;
    }
    ```
3.  **Создайте экземпляр класса**: Класс не требует конфигурации, так как работает с параметрами, передаваемыми из `BoxCalculation`.

    ```php
    use BoxCalculator\DeliveryCalculation;
    $deliveryCalculation = new DeliveryCalculation();
    ```
4. **Используйте методы**: Вызывайте методы для расчета логистики или стоимости доставки Ozon.

***

### 📋 Методы и использование

Класс `DeliveryCalculation` предоставляет методы для расчета логистики различных типов коробок и доставки через Ozon. Ниже описаны все методы.

#### 1. `getDeliveryOzon($ozonPrice, $amountBox)` 🚚

Рассчитывает стоимость доставки через Ozon.

* **Параметры**:
  * `$ozonPrice` (float): Цена одной коробки для Ozon (из калькуляции).
  * `$amountBox` (int): Количество коробок.
* **Возвращает**: `float` — стоимость доставки через Ozon.
* **Логика**:
  * Рассчитывает стоимость как 12.5% от общей стоимости заказа (`$ozonPrice * $amountBox`) плюс фиксированная надбавка 50.
  * Округляет результат вверх до целого числа.
*   **Пример**:

    ```php
    $ozonCost = $deliveryCalculation->getDeliveryOzon(100, 10);
    echo $ozonCost; // Вывод: 175 (ceil((100 * 10 * 0.125) + 50))
    ```

***

#### 2. `calculateLogistics427($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode)` 📦

Рассчитывает логистические параметры для коробки типа FEFCO 0427.

* **Параметры**:
  * `$WP` (int): Ширина развертки коробки, мм.
  * `$LP` (int): Длина развертки коробки, мм.
  * `$countStandartInPostbox` (int): Количество коробок в стандартной упаковке (из БД).
  * `$thicknessCardboard` (float): Толщина картона, мм.
  * `$costPostbox` (int): Стоимость упаковки (из БД).
  * `$priceMuulPlav` (float): Цена MUUL с плавающей комиссией (из калькуляции).
  * `$CSM` (float): Стоимость квадратного метра картона (из калькуляции).
  * `$amountBox` (int): Количество коробок.
  * `$totalVolWeightPostSDEK_MUUL` (float): Объемный вес заказа для СДЭК (из калькуляции).
  * `$priceMuulResult` (float): Итоговая цена MUUL (из калькуляции).
  * `$calculateDeliveryMode` (bool): Режим расчета доставки (если `true`, использует `$totalVolWeightPostSDEK_MUUL` и `$priceMuulResult`).
* **Возвращает**: Массив с логистическими параметрами (внутренние и внешние габариты, вес, стоимость упаковки, доставка СДЭК и т.д.).
* **Логика**:
  * Рассчитывает количество стандартных и последней упаковок.
  * Определяет внутренние и внешние габариты упаковок с учетом зазоров (6.5 мм по длине, 6.9 мм по ширине, 3 мм по высоте).
  * Вычисляет вес и объемный вес упаковок.
  * Рассчитывает стоимость упаковки, включая материалы, оборудование (20) и сборку (50).
  * Определяет стоимость доставки СДЭК (склад-склад и склад-дверь) с учетом объемного веса и страховки (0.75% от стоимости).
  * Учитывает НДС (1.2) для цен MUUL.
*   **Пример**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics427(
        600, 400, 10, 1.5, 50, 100, 0.01, 20, 5, 1000, false
    );
    print_r($logistics);
    ```

***

#### 3. `calculateLogistics201($lBox, $wBox, $hBox, $WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode)` 📦

Рассчитывает логистические параметры для коробки типа FEFCO 0201.

* **Параметры**:
  * Дополнительно: `$lBox` (int), `$wBox` (int), `$hBox` (int) — размеры коробки, мм.
  * Остальные параметры аналогичны `calculateLogistics427`.
* **Возвращает**: Массив с логистическими параметрами.
* **Логика**:
  * Аналогична `calculateLogistics427`, но использует размеры коробки (`$lBox`, `$wBox`, `$hBox`) для расчета внутренней ширины упаковки.
  * Учитывает утроенную толщину картона для высоты упаковки (`$hinPostboxStandart`).
*   **Пример**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics201(
        200, 150, 100, 600, 400, 10, 1.5, 50, 100, 0.01, 20, 5, 1000, false
    );
    print_r($logistics);
    ```

***

#### 4. `calculateLogistics215($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode, $lBox, $wBox, $hBox)` 📦

Рассчитывает логистические параметры для коробки типа FEFCO 0215.

* **Параметры**: Аналогичны `calculateLogistics201`.
* **Возвращает**: Массив с логистическими параметрами.
* **Логика**: Аналогична `calculateLogistics201`, с учетом уникальных размеров развертки и утроенной толщины для высоты упаковки.
*   **Пример**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics215(
        600, 400, 10, 1.5, 50, 100, 0.01, 20, 5, 1000, false, 200, 150, 100
    );
    print_r($logistics);
    ```

***

#### 5. `calculateLogistics330($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode, $lBox, $wBox, $hBox)` 📦

Рассчитывает логистические параметры для коробки типа FEFCO 0330.

* **Параметры**: Аналогичны `calculateLogistics201`.
* **Возвращает**: Массив с логистическими параметрами.
* **Логика**:
  * Использует размеры коробки и толщину картона для расчета внутренних габаритов с учетом специфики FEFCO 0330.
  * Выбирает минимальную ширину упаковки между двумя вариантами развертки.
  * Учитывает удвоенную толщину для высоты упаковки.
*   **Пример**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics330(
        600, 400, 10, 1.5, 50, 100, 0.01, 20, 5, 1000, false, 200, 150, 100
    );
    print_r($logistics);
    ```

***

#### 6. `calculateLogistics1006($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode, $lBox, $wBox, $hBox)` 📦

Рассчитывает логистические параметры для шестигранной коробки (1006).

* **Параметры**: Аналогичны `calculateLogistics201`.
* **Возвращает**: Массив с логистическими параметрами.
* **Логика**: Аналогична `calculateLogistics427`, но использует размеры развертки шестигранной коробки.
*   **Пример**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics1006(
        600, 400, 10, 1.5, 50, 100, 0.01, 20, 5, 1000, false, 200, 200, 100
    );
    print_r($logistics);
    ```

***

#### 7. `calculateLogistics933($WP, $LP, $countStandartInPostbox, $thicknessCardboard, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode, $lBox, $wBox, $hBox)` 📦

Рассчитывает логистические параметры для коробки типа 933 (ложемент).

* **Параметры**: Аналогичны `calculateLogistics201`.
* **Возвращает**: Массив с логистическими параметрами.
* **Логика**:
  * Аналогична `calculateLogistics427`, но использует размеры развертки ложемента.
  * Учитывает меньшую высоту заказа (`$totalHPostCM`).
*   **Пример**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics933(
        600, 400, 10, 1.5, 50, 100, 0.01, 20, 5, 1000, false, 50, 40, 100
    );
    print_r($logistics);
    ```

***

#### 8. `calculateLogisticsNonGenerative($typeBox, $costPostbox, $priceMuulPlav, $CSM, $amountBox, $totalVolWeightPostSDEK_MUUL, $priceMuulResult, $calculateDeliveryMode)` 📦

Рассчитывает логистические параметры для негенеративных коробок (1301, 1302, 1701).

* **Параметры**:
  * `$typeBox` (string): Тип коробки (1301, 1302, 1701).
  * Остальные параметры аналогичны `calculateLogistics427`, за исключением `$WP`, `$LP`, `$thicknessCardboard`, `$countStandartInPostbox`, `$lBox`, `$wBox`, `$hBox`.
* **Возвращает**: Массив с логистическими параметрами.
* **Логика**:
  * Использует фиксированные внутренние габариты для каждой модели (например, 663x593x30 мм для 1301).
  * Рассчитывает параметры упаковки и доставки аналогично другим методам.
*   **Пример**:

    ```php
    $logistics = $deliveryCalculation->calculateLogisticsNonGenerative(
        '1301', 50, 100, 0.01, 20, 5, 1000, false
    );
    print_r($logistics);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Методы не выбрасывают исключений, но могут возвращать некорректные результаты при неверных входных данных. Проверяйте параметры перед вызовом.

    ```php
    if ($amountBox > 0 && $ozonPrice > 0) {
        $ozonCost = $deliveryCalculation->getDeliveryOzon($ozonPrice, $amountBox);
    } else {
        echo 'Некорректные параметры';
    }
    ```
* **Валидация параметров**:
  * Убедитесь, что `$WP`, `$LP`, `$lBox`, `$wBox`, `$hBox`, `$amountBox` — положительные числа.
  * Проверьте, что `$typeBox` для `calculateLogisticsNonGenerative` — один из 1301, 1302, 1701.
  * Убедитесь, что `$countStandartInPostbox` и `$costPostbox` корректны (из БД).
* **Режим доставки**:
  * Используйте `$calculateDeliveryMode = true` для расчета с предопределенным объемным весом (`$totalVolWeightPostSDEK_MUUL`) и ценой (`$priceMuulResult`).
  * Для стандартного режима (`$calculateDeliveryMode = false`), убедитесь, что `$priceMuulPlav` корректен.
* **Оптимизация**:
  * Кэшируйте результаты вызовов для одинаковых параметров, если они не меняются часто.
  * Проверьте формулы расчета СДЭК (`$cdekPostSS`, `$cdekPostSD`, `$cdekMagSS`, `$cdekMagSD`) на актуальность тарифов.
* **Логирование**: Внедрите логирование результатов и входных параметров для анализа стоимости доставки и упаковки.
* **Точность расчетов**:
  * Проверьте значения зазоров (6.5 мм, 6.9 мм, 3 мм) и коэффициентов (например, 0.000035 для стоимости упаковки) на соответствие реальным данным.
  * Убедитесь, что плотность картона (270 г/м²) актуальна.

***

### 🐛 Устранение неполадок

* **Некорректные габариты**:
  * Проверьте, что `$WP`, `$LP`, `$lBox`, `$wBox`, `$hBox` соответствуют данным калькуляции.
  * Убедитесь, что `$thicknessCardboard` — положительное значение.
* **Нулевая стоимость упаковки**:
  * Проверьте `$costPostbox`, `$countStandartInPostbox` и размеры упаковки (`$linPostboxStandart`, `$winPostboxStandart`).
  * Убедитесь, что `$amountBox` > 0.
* **Ошибки доставки СДЭК**:
  * Проверьте, что `$totalVolWeightPostSDEK_MUUL` корректен при `$calculateDeliveryMode = true`.
  * Убедитесь, что формулы тарифов СДЭК актуальны (например, 210 + 29.9 \* вес для `cdekPostSS`).
* **Некорректная стоимость Ozon**:
  * Проверьте, что `$ozonPrice` и `$amountBox` — положительные числа.
  * Убедитесь, что формула (12.5% + 50) соответствует текущим тарифам Ozon.
* **Ошибки негенеративных коробок**:
  * Проверьте, что `$typeBox` — один из 1301, 1302, 1701.
  * Убедитесь, что фиксированные габариты (например, 663x593x30 для 1301) актуальны.

***
