---
icon: php
---

# DeliveryCalculationInterface

## 🚚 Документация по интерфейсу DeliveryCalculationInterface

Добро пожаловать в документацию по интерфейсу `DeliveryCalculationInterface`! 🎉 Этот PHP-интерфейс, расположенный в пространстве имен `BoxCalculator\Interfaces`, определяет контракт для классов, которые выполняют расчет логистических параметров и стоимости доставки для коробок различных типов (FEFCO и негенеративных) в системе калькуляции коробок. Интерфейс включает методы для расчета доставки через Ozon и СДЭК, а также для определения параметров упаковки. Ниже вы найдете подробное описание методов интерфейса, их назначение и рекомендации по реализации.

***

### 🚀 Обзор

Интерфейс `DeliveryCalculationInterface` разработан для обеспечения единообразного подхода к расчету логистики, включая:

* Расчет стоимости доставки через Ozon на основе цены и количества коробок 📦
* Вычисление логистических параметров для коробок типов 427, 201, 215, 330, 1006, 426, 933 и негенеративных коробок (1301, 1302, 1701) 🚚
* Учет размеров развертки, толщины картона, количества коробок и параметров упаковки 📏
* Поддержка режимов расчета доставки через СДЭК (склад-склад и склад-дверь) с учетом объемного веса и страховки 🔧

Интерфейс предназначен для реализации в классах, таких как `DeliveryCalculation`, которые интегрируются с системой калькуляции коробок (`BoxCalculation`) и обеспечивают точные расчеты стоимости доставки и упаковки.

***

### 🛠️ Установка и настройка

Чтобы использовать интерфейс `DeliveryCalculationInterface`, выполните следующие шаги:

1.  **Подключите интерфейс**: Убедитесь, что файл интерфейса включен в ваш PHP-проект.

    ```php
    require_once 'path/to/DeliveryCalculationInterface.php';
    ```
2.  **Создайте реализующий класс**: Реализуйте интерфейс в классе, например, `DeliveryCalculation`, который будет выполнять логистические расчеты.

    ```php
    namespace BoxCalculator;

    use BoxCalculator\Interfaces\DeliveryCalculationInterface;

    class DeliveryCalculation implements DeliveryCalculationInterface {
        // Реализация методов интерфейса (см. ниже)
    }
    ```
3.  **Интеграция с калькуляцией**: Убедитесь, что реализующий класс интегрируется с `BoxCalculation`, получая необходимые параметры (например, `$WP`, `$LP`, `$ozonPrice`).

    ```php
    $deliveryCalculation = new DeliveryCalculation();
    ```
4. **Используйте методы**: Вызывайте методы реализующего класса для расчета логистики или стоимости доставки.

***

### 📋 Методы интерфейса

Интерфейс `DeliveryCalculationInterface` определяет девять методов, которые должны быть реализованы в классах. Ниже описаны их назначение, параметры, возвращаемые значения и рекомендации по реализации.

#### 1. `getDeliveryOzon(float $ozonPrice, int $amountBox): float` 🚚

Рассчитывает стоимость доставки через Ozon.

* **Параметры**:
  * `$ozonPrice` (float): Цена одной коробки для Ozon (из калькуляции).
  * `$amountBox` (int): Количество коробок.
* **Возвращает**: `float` — стоимость доставки через Ozon.
* **Рекомендации по реализации**:
  * Рассчитайте стоимость как процент (например, 12.5%) от общей стоимости заказа (`$ozonPrice * $amountBox`) плюс фиксированную надбавку (например, 50).
  * Округлите результат вверх до целого числа с помощью `ceil`.
  * Проверьте, что `$ozonPrice` и `$amountBox` — положительные значения.
*   **Пример использования**:

    ```php
    $ozonCost = $deliveryCalculation->getDeliveryOzon(100.0, 10);
    echo $ozonCost; // Вывод: 175.0 (ceil((100 * 10 * 0.125) + 50))
    ```

***

#### 2. `calculateLogistics427(int $WP, int $LP, int $countStandartInPostbox, float $thicknessCardboard, int $costPostbox, float $priceMullPlav, float $CSM, int $amountBox, float $totalVolWeightPostSDEK_MUUL, float $priceMuulResult, bool $calculateDeliveryMode): array` 📦

Рассчитывает логистические параметры для коробки типа FEFCO 0427.

* **Параметры**:
  * `$WP` (int): Ширина развертки коробки, мм.
  * `$LP` (int): Длина развертки коробки, мм.
  * `$countStandartInPostbox` (int): Количество коробок в стандартной упаковке (из БД).
  * `$thicknessCardboard` (float): Толщина картона, мм.
  * `$costPostbox` (int): Стоимость упаковки (из БД).
  * `$priceMullPlav` (float): Цена MUUL с плавающей комиссией (из калькуляции).
  * `$CSM` (float): Стоимость квадратного метра картона (из калькуляции).
  * `$amountBox` (int): Количество коробок.
  * `$totalVolWeightPostSDEK_MUUL` (float): Объемный вес заказа для СДЭК (из калькуляции).
  * `$priceMuulResult` (float): Итоговая цена MUUL (из калькуляции).
  * `$calculateDeliveryMode` (bool): Режим расчета доставки (если `true`, использует `$totalVolWeightPostSDEK_MUUL` и `$priceMuulResult`).
* **Возвращает**: Массив с логистическими параметрами (внутренние и внешние габариты, вес, стоимость упаковки, доставка СДЭК и т.д.).
* **Рекомендации по реализации**:
  * Рассчитайте количество стандартных и последней упаковок на основе `$amountBox` и `$countStandartInPostbox`.
  * Определите внутренние габариты упаковок, используя `$WP`, `$LP` и `$thicknessCardboard`.
  * Добавьте зазоры (например, 6.5 мм по длине, 6.9 мм по ширине, 3 мм по высоте) для внешних габаритов.
  * Вычислите вес упаковок, используя плотность картона (например, 270 г/м²).
  * Рассчитайте объемный вес (`length * width * height / 5000`).
  * Определите стоимость упаковки, включая материалы, оборудование (например, 20) и сборку (например, 50).
  * Рассчитайте стоимость доставки СДЭК для режимов склад-склад (`cdekPostSS`, `cdekMagSS`) и склад-дверь (`cdekPostSD`, `cdekMagSD`), учитывая объемный вес и страховку (например, 0.75% от стоимости).
  * Учитывайте НДС (например, 1.2) для цен MUUL (`pricePostMUULSS`, `pricePostMUULSD`).
  * Если `$calculateDeliveryMode = true`, используйте `$totalVolWeightPostSDEK_MUUL` и `$priceMuulResult` для расчета.
*   **Пример использования**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics427(
        600, 400, 10, 1.5, 50, 100.0, 0.01, 20, 5.0, 1000.0, false
    );
    print_r($logistics);
    ```

***

#### 3. `calculateLogisticsNonGenerative(string $typeBox, int $costPostbox, float $priceMullPlav, float $CSM, int $amountBox, float $totalVolWeightPostSDEK_MUUL, float $priceMuulResult, bool $calculateDeliveryMode): array` 📦

Рассчитывает логистические параметры для негенеративных коробок (1301, 1302, 1701).

* **Параметры**:
  * `$typeBox` (string): Тип коробки (1301, 1302, 1701).
  * `$costPostbox` (int): Стоимость упаковки (из БД).
  * `$priceMullPlav` (float): Цена MUUL с плавающей комиссией (из калькуляции).
  * `$CSM` (float): Стоимость квадратного метра картона (из калькуляции).
  * `$amountBox` (int): Количество коробок.
  * `$totalVolWeightPostSDEK_MUUL` (float): Объемный вес заказа для СДЭК (из калькуляции).
  * `$priceMuulResult` (float): Итоговая цена MUUL (из калькуляции).
  * `$calculateDeliveryMode` (bool): Режим расчета доставки.
* **Возвращает**: Массив с логистическими параметрами.
* **Рекомендации по реализации**:
  * Используйте фиксированные внутренние габариты для каждого типа коробки (например, 663x593x30 мм для 1301).
  * Рассчитайте количество упаковок и габариты аналогично `calculateLogistics427`.
  * Вычислите вес, объемный вес и стоимость доставки СДЭК, как в других методах.
  * Учитывайте `$calculateDeliveryMode` для выбора объемного веса и цены.
*   **Пример использования**:

    ```php
    $logistics = $deliveryCalculation->calculateLogisticsNonGenerative(
        '1301', 50, 100.0, 0.01, 20, 5.0, 1000.0, false
    );
    print_r($logistics);
    ```

***

#### 4. `calculateLogistics201(int $lBox, int $wBox, int $hBox, int $WP, int $LP, int $countStandartInPostbox, float $thicknessCardboard, int $costPostbox, float $priceMullPlav, float $CSM, int $amountBox, float $totalVolWeightPostSDEK_MUUL, float $priceMuulResult, bool $calculateDeliveryMode): array` 📦

Рассчитывает логистические параметры для коробки типа FEFCO 0201.

* **Параметры**:
  * `$lBox` (int): Длина коробки, мм.
  * `$wBox` (int): Ширина коробки, мм.
  * `$hBox` (int): Высота коробки, мм.
  * Остальные параметры аналогичны `calculateLogistics427`.
* **Возвращает**: Массив с логистическими параметрами.
* **Рекомендации по реализации**:
  * Используйте `$lBox`, `$wBox`, `$hBox` для расчета внутренней ширины упаковки.
  * Учитывайте утроенную толщину картона для высоты упаковки (`hinPostboxStandart`).
  * Следуйте общей логике `calculateLogistics427` для расчета габаритов, веса и стоимости доставки.
*   **Пример использования**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics201(
        200, 150, 100, 600, 400, 10, 1.5, 50, 100.0, 0.01, 20, 5.0, 1000.0, false
    );
    print_r($logistics);
    ```

***

#### 5. `calculateLogistics215(int $WP, int $LP, int $countStandartInPostbox, float $thicknessCardboard, int $costPostbox, float $priceMullPlav, float $CSM, int $amountBox, float $totalVolWeightPostSDEK_MUUL, float $priceMuulResult, bool $calculateDeliveryMode, int $lBox, int $wBox, int $hBox): array` 📦

Рассчитывает логистические параметры для коробки типа FEFCO 0215.

* **Параметры**: Аналогичны `calculateLogistics201`.
* **Возвращает**: Массив с логистическими параметрами.
* **Рекомендации по реализации**:
  * Аналогично `calculateLogistics201`, с учетом уникальных размеров развертки и утроенной толщины для высоты упаковки.
  * Используйте `$lBox`, `$wBox`, `$hBox` для точного расчета габаритов.
*   **Пример использования**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics215(
        600, 400, 10, 1.5, 50, 100.0, 0.01, 20, 5.0, 1000.0, false, 200, 150, 100
    );
    print_r($logistics);
    ```

***

#### 6. `calculateLogistics330(int $WP, int $LP, int $countStandartInPostbox, float $thicknessCardboard, int $costPostbox, float $priceMullPlav, float $CSM, int $amountBox, float $totalVolWeightPostSDEK_MUUL, float $priceMuulResult, bool $calculateDeliveryMode, int $lBox, int $wBox, int $hBox): array` 📦

Рассчитывает логистические параметры для коробки типа FEFCO 0330.

* **Параметры**: Аналогичны `calculateLogistics201`.
* **Возвращает**: Массив с логистическими параметрами.
* **Рекомендации по реализации**:
  * Используйте `$lBox`, `$wBox`, `$hBox` и `$thicknessCardboard` для расчета внутренних габаритов с учетом специфики FEFCO 0330.
  * Выберите минимальную ширину упаковки между двумя вариантами развертки.
  * Учитывайте удвоенную толщину картона для высоты упаковки.
  * Следуйте общей логике `calculateLogistics427` для остальных расчетов.
*   **Пример использования**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics330(
        600, 400, 10, 1.5, 50, 100.0, 0.01, 20, 5.0, 1000.0, false, 200, 150, 100
    );
    print_r($logistics);
    ```

***

#### 7. `calculateLogistics1006(int $WP, int $LP, int $countStandartInPostbox, float $thicknessCardboard, int $costPostbox, float $priceMullPlav, float $CSM, int $amountBox, float $totalVolWeightPostSDEK_MUUL, float $priceMuulResult, bool $calculateDeliveryMode, int $lBox, int $wBox, int $hBox): array` 📦

Рассчитывает логистические параметры для шестигранной коробки (1006).

* **Параметры**: Аналогичны `calculateLogistics201`.
* **Возвращает**: Массив с логистическими параметрами.
* **Рекомендации по реализации**:
  * Используйте размеры развертки (`$WP`, `$LP`) шестигранной коробки для расчета габаритов.
  * Следуйте общей логике `calculateLogistics427`, адаптируя расчеты под специфику шестигранной формы.
*   **Пример использования**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics1006(
        600, 400, 10, 1.5, 50, 100.0, 0.01, 20, 5.0, 1000.0, false, 200, 200, 100
    );
    print_r($logistics);
    ```

***

#### 8. `calculateLogistics933(int $WP, int $LP, int $countStandartInPostbox, float $thicknessCardboard, int $costPostbox, float $priceMullPlav, float $CSM, int $amountBox, float $totalVolWeightPostSDEK_MUUL, float $priceMuulResult, bool $calculateDeliveryMode, int $lBox, int $wBox, int $hBox): array` 📦

Рассчитывает логистические параметры для коробки типа 933 (ложемент).

* **Параметры**: Аналогичны `calculateLogistics201`.
* **Возвращает**: Массив с логистическими параметрами.
* **Рекомендации по реализации**:
  * Используйте `$WP`, `$LP` для расчета габаритов ложемента.
  * Учитывайте меньшую высоту заказа (`totalHPostCM`) по сравнению с другими типами коробок.
  * Следуйте общей логике `calculateLogistics427`, адаптируя расчеты под ложементы.
*   **Пример использования**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics933(
        600, 400, 10, 1.5, 50, 100.0, 0.01, 20, 5.0, 1000.0, false, 50, 40, 100
    );
    print_r($logistics);
    ```

***

#### 9. `calculateLogistics426(int $WP, int $LP, int $countStandartInPostbox, float $thicknessCardboard, int $costPostbox, float $priceMullPlav, float $CSM, int $amountBox, float $totalVolWeightPostSDEK_MUUL, float $priceMuulResult, bool $calculateDeliveryMode, int $lBox, int $wBox, int $hBox): array` 📦

Рассчитывает логистические параметры для коробки типа FEFCO 0426.

* **Параметры**: Аналогичны `calculateLogistics201`.
* **Возвращает**: Массив с логистическими параметрами.
* **Рекомендации по реализации**:
  * Используйте `$WP`, `$LP` и размеры коробки (`$lBox`, `$wBox`, `$hBox`) для расчета габаритов с учетом специфики FEFCO 0426.
  * Следуйте общей логике `calculateLogistics427`, адаптируя расчеты под уникальные размеры развертки.
*   **Пример использования**:

    ```php
    $logistics = $deliveryCalculation->calculateLogistics426(
        600, 400, 10, 1.5, 50, 100.0, 0.01, 20, 5.0, 1000.0, false, 200, 150, 100
    );
    print_r($logistics);
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Реализуйте проверку входных параметров и обработку некорректных данных, чтобы избежать ошибок в расчетах.

    ```php
    public function getDeliveryOzon(float $ozonPrice, int $amountBox): float {
        if ($ozonPrice <= 0 || $amountBox <= 0) {
            throw new InvalidArgumentException('Price and amount must be positive');
        }
        return ceil(($ozonPrice * $amountBox * 0.125) + 50);
    }
    ```
* **Валидация параметров**:
  * Проверяйте, что `$WP`, `$LP`, `$lBox`, `$wBox`, `$hBox`, `$countStandartInPostbox`, `$costPostbox`, `$amountBox` — положительные числа.
  * Убедитесь, что `$thicknessCardboard`, `$priceMullPlav`, `$CSM`, `$totalVolWeightPostSDEK_MUUL`, `$priceMuulResult` — неотрицательные.
  * Для `calculateLogisticsNonGenerative` валидируйте, что `$typeBox` — один из 1301, 1302, 1701.
* **Оптимизация**:
  * Кэшируйте результаты расчетов для одинаковых входных параметров, если они не меняются часто.
  * Проверьте актуальность тарифов СДЭК и Ozon в формулах (например, 210 + 29.9 \* вес для `cdekPostSS`).
* **Логирование**: Внедрите логирование входных параметров и результатов для анализа стоимости доставки и упаковки.
* **Точность расчетов**:
  * Проверьте значения зазоров (например, 6.5 мм, 6.9 мм, 3 мм) и коэффициентов (например, 0.000035 для стоимости упаковки) на соответствие реальным данным.
  * Убедитесь, что плотность картона (например, 270 г/м²) актуальна.
* **Режим доставки**:
  * Используйте `$calculateDeliveryMode = true` для расчетов с предопределенным объемным весом и ценой.
  * Для `$calculateDeliveryMode = false`, убедитесь, что `$priceMullPlav` корректен.

***

### 🐛 Устранение неполадок

* **Некорректные габариты**:
  * Проверьте, что `$WP`, `$LP`, `$lBox`, `$wBox`, `$hBox` соответствуют данным калькуляции.
  * Убедитесь, что `$thicknessCardboard` — положительное значение.
* **Нулевая стоимость упаковки**:
  * Проверьте `$costPostbox`, `$countStandartInPostbox` и размеры упаковки.
  * Убедитесь, что `$amountBox` > 0.
* **Ошибки доставки СДЭК**:
  * Проверьте корректность `$totalVolWeightPostSDEK_MUUL` при `$calculateDeliveryMode = true`.
  * Убедитесь, что формулы тарифов СДЭК актуальны.
* **Ошибки доставки Ozon**:
  * Проверьте, что `$ozonPrice` и `$amountBox` — положительные числа.
  * Убедитесь, что формула (например, 12.5% + 50) соответствует текущим тарифам Ozon.
* **Ошибки негенеративных коробок**:
  * Проверьте, что `$typeBox` — один из 1301, 1302, 1701.
  * Убедитесь, что фиксированные габариты для каждого типа актуальны.

***
