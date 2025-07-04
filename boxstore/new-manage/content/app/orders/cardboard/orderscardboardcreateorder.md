---
icon: php
---

# ordersCardboardCreateOrder

## 📄 Документация по файлу `ordersCardboardCreateOrder.php` (АдминПанель)

Файл `ordersCardboardCreateOrder.php` является модулем веб-приложения **АдминПанель**, расположенным в директории `content/app/orders/cardboard`. Он отвечает за создание нового заказа на картонные коробки, включая выбор типа коробки, цвета, размеров, количества, типа заказа, ложемента, а также настройку логистики и информации о клиенте. Файл сочетает серверную логику на PHP с клиентским интерфейсом, использующим HTML, CSS, JavaScript и библиотеки Alpine.js, Bootstrap, Chart.js, и виджет СДЭК. Документация описывает структуру, функциональность и назначение файла в контексте административной панели.

***

### 🚀 Обзор

Файл `ordersCardboardCreateOrder.php` выполняет следующие задачи:

* Проверяет права доступа пользователя через флаг `ORDERS_CARDBOARD_CREATEORDER`.
* Извлекает данные из баз данных для типов коробок, цветов картона, типов заказов и ложементов.
* Отображает интерактивный интерфейс для добавления позиций в заказ, управления корзиной и настройки логистики.
* Обрабатывает расчет цен (Muul, Boxstore, Ozon) через AJAX-запросы.
* Интегрируется с виджетом СДЭК для выбора пункта выдачи (ПВЗ) и с сервисом Dadata для автодополнения адресов.
* Создает заказ в CRM через API, сохраняя данные о товарах, клиенте и доставке.
* Генерирует PDF-документ с информацией о заказе.

***

### 📋 Структура и функциональность

#### 1. **Проверка прав доступа и серверная логика**

```php
if ($flags->hasAnyFlag(['ORDERS_CARDBOARD_CREATEORDER'])) {
    $typeBoxes = $read->query("SELECT typeBox, descriptionType FROM types_boxes")->fetchAll(PDO::FETCH_ASSOC);
    $colors = array_values(array_unique(
        $cardboard_pdo->query("SELECT colorCardboard, typeCardboard, colorCardboardEn FROM cardboardStore")
            ->fetchAll(PDO::FETCH_ASSOC),
        SORT_REGULAR
    ));
    $orderTypes = $read->query("SELECT typeOrder, days, name, markup FROM type_orders ORDER BY days DESC")
        ->fetchAll(PDO::FETCH_ASSOC);
    $cellsType = $prod_formulas_drawings->query("SELECT `amount`, `cell_number_l`, `cell_number_w` FROM cells_0933")->fetchAll(PDO::FETCH_ASSOC);
    $cels = [];
    foreach ($cellsType as $cell) {
        $cels[$cell['amount']] = $cell['amount'] . ' ('.$cell['cell_number_l']. 'x' . $cell['cell_number_w'] . ')';
    }
?>
```

* **Описание**:
  * **Проверка прав**: Использует объект `$flags` для проверки наличия флага `ORDERS_CARDBOARD_CREATEORDER`. Если флаг отсутствует, содержимое файла не выполняется.
  * **Запросы к базе данных**:
    * Извлекает типы коробок (`typeBox`, `descriptionType`) из таблицы `types_boxes` через PDO-объект `$read`.
    * Получает уникальные цвета картона (`colorCardboard`, `typeCardboard`, `colorCardboardEn`) из таблицы `cardboardStore` через PDO-объект `$cardboard_pdo`.
    * Извлекает типы заказов (`typeOrder`, `days`, `name`, `markup`) из таблицы `type_orders`, сортируя по убыванию дней, через `$read`.
    * Получает данные о ложементах (`amount`, `cell_number_l`, `cell_number_w`) для коробок типа 933 из таблицы `cells_0933` через PDO-объект `$prod_formulas_drawings`.
  * **Формирование массива `$cels`**: Создает ассоциативный массив, где ключ — количество ячеек (`amount`), а значение — строка вида `<количество> (<cell_number_l>x<cell_number_w>)`.
* **Назначение**: Обеспечивает доступ к модулю только для пользователей с соответствующими правами и подготавливает данные для клиентского интерфейса.

***

#### 2. **Подключение скриптов и стилей**

```php
<script src="https://cdn.jsdelivr.net/npm/@cdek-it/widget@3"></script>
<script src="<?= $path ?>/assets/app/js/createOrder/cdek.js?v=<?= time() ?>"></script>
<style>
    .suggestions-list { ... }
    .suggestions-list li { ... }
    .suggestions-list li:hover { ... }
</style>
<style>
    .form-input.is-invalid, .was-validated .form-input:invalid { ... }
    .form-input.is-valid, .was-validated .form-input:valid { ... }
</style>
<script src="https://unpkg.com/imask"></script>
```

* **Описание**:
  * **Скрипты**:
    * Подключает виджет СДЭК версии 3 для выбора ПВЗ (`@cdek-it/widget@3`).
    * Подключает локальный скрипт `cdek.js` из `assets/app/js/createOrder/` с параметром версии (`v=<?= time() ?>`) для предотвращения кэширования.
    * Подключает библиотеку IMask для форматирования ввода телефона.
  * **Стили**:
    * Определяет стили для списка автодополнения адресов (`.suggestions-list`), включая оформление элементов и ховер-эффект.
    * Определяет стили для валидации полей ввода (`.form-input.is-invalid`, `.form-input.is-valid`) с использованием SVG-иконок для отображения статуса (ошибка/успех).
* **Назначение**: Подготавливает клиентские ресурсы для интерактивного интерфейса и валидации данных.

***

#### 3. **Клиентский интерфейс**

```html
<div x-data="sales">
    <h2 class="text-2xl font-semibold mt-5 mb-5">Создание заказа</h2>

    <!-- Форма добавления позиции -->
    <div class="panel mt-5" x-data="singleItemForm" @edit-item.window="Object.assign($data, $event.detail)">
        <h5 class="text-lg font-semibold">Добавление позиции</h5>
        <form @submit.prevent="addItem"> ... </form>
    </div>

    <!-- Корзина -->
    <div class="panel mt-5" x-data="cartModal"> ... </div>

    <!-- Логистика -->
    <div class="panel mt-5" x-data="logisticForm"> ... </div>
</div>
```

* **Описание**:
  * Интерфейс разделен на три секции: добавление позиции, корзина и логистика.
  * **Форма добавления позиции**:
    * Позволяет выбрать тип коробки, цвет картона, размеры (длина, ширина, высота), количество, тип заказа и ложемент.
    * Отображает рассчитанные цены для Muul, Boxstore и Ozon в таблице.
    * Поддерживает режим разработчика (`developerMode`) для дополнительных настроек.
  * **Корзина**:
    * Показывает добавленные позиции с возможностью редактирования или удаления.
    * Отображает стоимость доставки (самовывоз, ПВЗ, до двери) и итоговую сумму.
    * Позволяет очистить корзину или сгенерировать PDF.
  * **Логистика**:
    * Настраивает способ доставки (самовывоз, ПВЗ, до двери).
    * Интегрируется с виджетом СДЭК для выбора ПВЗ.
    * Поддерживает автодополнение адресов через Dadata.
    * Собирает данные о клиенте (физическое/юридическое лицо, ФИО, телефон, email, промокод).
  * Использует Alpine.js для управления состоянием и интерактивностью (компоненты `sales`, `singleItemForm`, `cartModal`, `logisticForm`).
  * Применяет Bootstrap для стилизации (классы `panel`, `btn`, `form-input`, `table`).
* **Назначение**: Обеспечивает удобный интерфейс для создания заказа, управления корзиной и настройки доставки.

***

#### 4. **JavaScript-логика (Alpine.js)**

```javascript
<script>
    let emailDebounceTimer = null;
    const initialData = {
        typeBoxes: <?= json_encode($typeBoxes) ?>,
        colors: <?= json_encode($colors) ?>,
        orderTypes: <?= json_encode($orderTypes) ?>,
        path: '<?= $path ?>',
        cellsType: <?= json_encode($cels) ?>
    };

    function declineDay(days) { ... }

    document.addEventListener('alpine:init', () => {
        Alpine.store('deliveryPrices', { ... });
        Alpine.data('sales', () => ({ ... }));
        Alpine.data('singleItemForm', () => ({ ... }));
        Alpine.data('cartModal', () => ({ ... }));
        Alpine.data('logisticForm', () => ({ ... }));
    });
</script>
```

* **Описание**:
  * **Передача данных**: PHP-данные (`typeBoxes`, `colors`, `orderTypes`, `path`, `cels`) передаются в JavaScript через `json_encode`.
  * **Хранилище Alpine.js**:
    * `deliveryPrices`: Глобальное хранилище для цен доставки (самовывоз, ПВЗ, до двери).
  * **Компоненты Alpine.js**:
    * `sales`: Общий контейнер, инициализирует компонент.
    * `singleItemForm`: Управляет формой добавления позиции, включая выбор типа коробки, цвета, расчет цен через AJAX (`calcBox.php`), добавление в корзину.
    * `cartModal`: Управляет корзиной, отображая позиции, рассчитывая стоимость доставки через AJAX (`calcDelivery.php`), поддерживая редактирование, удаление и генерацию PDF.
    * `logisticForm`: Обрабатывает форму логистики, включая выбор способа доставки, интеграцию с СДЭК, автодополнение адресов (Dadata), валидацию телефона и email, создание заказа через AJAX (`createOrder.php`).
  * **Вспомогательные функции**:
    * `declineDay`: Склоняет слово "день" для отображения сроков заказа.
    * Валидация телефона и email через AJAX (`checkPhone.php`, `checkEmail.php`).
    * Генерация PDF через запрос к `createPDF.php`.
* **Назначение**: Обеспечивает интерактивность интерфейса, асинхронные запросы и обработку данных.

***

### 🔗 Зависимости

Файл зависит от следующих компонентов:

* **Серверные объекты**:
  * `$flags`: Объект для проверки прав доступа (`hasAnyFlag`).
  * `$read`: PDO-объект для запросов к базе данных (таблицы `types_boxes`, `type_orders`).
  * `$cardboard_pdo`: PDO-объект для запросов к таблице `cardboardStore`.
  * `$prod_formulas_drawings`: PDO-объект для запросов к таблице `cells_0933`.
* **Файлы**:
  * `assets/app/js/createOrder/cdek.js`: Скрипт для работы с виджетом СДЭК.
  * `assets/app/php/orders/cardboard/calcBox.php`: Расчет цен для коробки.
  * `assets/app/php/calcDelivery.php`: Расчет стоимости доставки.
  * `assets/app/php/dadata/suggestions.php`: Автодополнение адресов через Dadata.
  * `assets/app/php/checkPhone.php`: Валидация телефона.
  * `assets/app/php/checkEmail.php`: Валидация email.
  * `assets/app/php/orders/cardboard/createOrder.php`: Создание заказа в CRM.
  * `https://cloud.muul.ru/manage/createorder/php/createPDF.php`: Генерация PDF.
* **Библиотеки**:
  * **Bootstrap**: Для стилизации интерфейса.
  * **Alpine.js**: Для управления состоянием и интерактивностью.
  * **IMask**: Для форматирования ввода телефона.
  * **Toastify**: Для отображения уведомлений.
  * **SweetAlert2 (Swal)**: Для модальных окон.
  * **jQuery**: Для AJAX-запросов и манипуляций с DOM.
  * **CDEK Widget**: Для выбора ПВЗ.

***

### 📑 Роль в АдминПанели

Файл `ordersCardboardCreateOrder.php` выполняет следующие функции в контексте АдминПанели:

* **Создание заказов**: Позволяет пользователям формировать заказы на картонные коробки, выбирая тип, цвет, размеры, количество и ложемент.
* **Интеграция с логистикой**: Поддерживает выбор способа доставки (самовывоз, ПВЗ, до двери) с интеграцией СДЭК и Dadata.
* **Калькуляция цен**: Рассчитывает цены для разных платформ (Muul, Boxstore, Ozon) в реальном времени.
* **Управление корзиной**: Хранит позиции в `localStorage`, поддерживает редактирование и удаление.
* **Интеграция с CRM**: Создает заказ в RetailCRM через API.
* **Генерация документов**: Формирует PDF с деталями заказа.
* **Безопасность**: Использует CSRF-токены для защиты AJAX-запросов.

***

### ⚙️ Технические детали

* **Сессии**: Проверяет `$_SESSION['user']` через `$flags` для контроля доступа.
* **Базы данных**: Использует PDO для запросов к таблицам `types_boxes`, `cardboardStore`, `type_orders`, `cells_0933`.
* **AJAX**: Обрабатывает асинхронные запросы к скриптам в `assets/app/php` для расчета цен, доставки, валидации и создания заказа.
* **Интерфейс**: Построен на Bootstrap с интерактивностью через Alpine.js, уведомлениями (Toastify) и модальными окнами (SweetAlert2).
* **Валидация**: Проверяет телефон и email через серверные скрипты, использует IMask для форматирования.
* **Интеграции**: RetailCRM, СДЭК, Dadata, внешний сервис для PDF.

***
