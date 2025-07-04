---
icon: php
---

# ordersCardboardEditItems

## 📄 Документация по файлу `ordersCardboardEditItems.php` (АдминПанель)

Файl `ordersCardboardEditItems.php` является модулем веб-приложения **АдминПанель**, расположенным в директории `content/app/orders/cardboard`. Он предназначен для редактирования состава заказа на картонные коробки, позволяя пользователю искать заказ по номеру, добавлять, редактировать или удалять позиции в корзине, а также сохранять изменения. Файл сочетает серверную логику на PHP с клиентским интерфейсом, использующим HTML, CSS, JavaScript, Alpine.js, Bootstrap, jQuery, SweetAlert2 и Toastify. Документация описывает структуру, функциональность и назначение файла в контексте административной панели.

***

### 🚀 Обзор

Файл `ordersCardboardEditItems.php` выполняет следующие задачи:

* Проверяет права доступа пользователя через флаг `ORDERS_CARDBOARD_EDITITEMS`.
* Извлекает данные о типах коробок, цветах картона, типах заказов и ложементах из базы данных.
* Отображает интерфейс для поиска заказа по номеру и редактирования его состава (добавление, изменение, удаление позиций).
* Рассчитывает цены для платформ Muul, Boxstore и Ozon через AJAX-запросы.
* Поддерживает управление корзиной с сохранением данных в `localStorage`.
* Рассчитывает стоимость доставки (самовывоз, ПВЗ, до двери) через AJAX-запросы.
* Сохраняет обновленный состав заказа в CRM через AJAX-запросы.
* Использует CSRF-токены для защиты запросов.

***

### 📋 Структура и функциональность

#### 1. **Проверка прав доступа и серверная логика**

```php
<?php if ($flags->hasAnyFlag(['ORDERS_CARDBOARD_EDITITEMS'])) {
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
  * **Проверка прав**: Проверяет наличие флага `ORDERS_CARDBOARD_EDITITEMS` с помощью объекта `$flags` и метода `hasAnyFlag`. Если флаг отсутствует, содержимое файла не отображается.
  * **Запросы к базе данных**:
    * Извлекает типы коробок (`typeBox`, `descriptionType`) из таблицы `types_boxes` через PDO-объект `$read`.
    * Получает уникальные цвета картона (`colorCardboard`, `typeCardboard`, `colorCardboardEn`) из таблицы `cardboardStore` через PDO-объект `$cardboard_pdo`.
    * Извлекает типы заказов (`typeOrder`, `days`, `name`, `markup`) из таблицы `type_orders`, сортируя по убыванию дней, через `$read`.
    * Получает данные о ложементах (`amount`, `cell_number_l`, `cell_number_w`) для коробок типа 933 из таблицы `cells_0933` через PDO-объект `$prod_formulas_drawings`.
  * **Формирование массива `$cels`**: Создает ассоциативный массив, где ключ — количество ячеек (`amount`), а значение — строка вида `<количество> (<cell_number_l>x<cell_number_w>)`.
* **Назначение**: Обеспечивает доступ к модулю только для пользователей с соответствующими правами и подготавливает данные для клиентского интерфейса.

***

#### 2. **Клиентский интерфейс**

```html
<div x-data="OrderManager()" x-init="init()">
    <ul class="flex space-x-2"> ... </ul>
    <h2 class="text-2xl font-semibold mt-5 mb-5">Редактирование состава заказа с коробками</h2>

    <div class="panel mt-5">
        <div class="flex items-center">
            <input id="orderNumber" type="text" class="form-input" placeholder="Введите номер заказа">
            <button id="findOrderBtn" class="btn btn-secondary">Найти</button>
        </div>
    </div>

    <div id="editable" style="display:none">
        <div class="panel mt-5">
            <h5 class="text-lg font-semibold">Добавление позиции</h5>
            <form id="singleAddForm" @submit.prevent="addItem()"> ... </form>
        </div>

        <div class="panel mt-5">
            <h5 class="text-lg font-semibold">Корзина</h5>
            <table class="table table-bordered" id="itemsTable"> ... </table>
            <div class="mt-3 flex items-center justify-between"> ... </div>
        </div>

        <button type="button" class="btn btn-primary mt-5" @click="saveItems()">Сохранить</button>
    </div>
</div>
```

* **Описание**:
  * **Компонент Alpine.js**: Использует компонент `OrderManager` для управления состоянием.
  * **Хлебные крошки**: Навигационная панель с ссылками на разделы "Заказы", "Картон" и текущей страницей "Редактировать состав".
  * **Поиск заказа**:
    * Поле ввода (`orderNumber`) для номера заказа и кнопка "Найти".
  * **Форма редактирования** (`editable`, скрыта по умолчанию):
    * **Добавление позиции** (`singleAddForm`):
      * Выбор типа коробки, цвета картона, размеров (длина, ширина, высота), количества, типа заказа и ложемента.
      * Таблица для отображения цен (Muul, Boxstore, Ozon).
    * **Корзина** (`itemsTable`):
      * Показывает добавленные позиции с возможностью редактирования или удаления.
      * Отображает стоимость доставки (самовывоз, ПВЗ, до двери) и итоговую сумму.
      * Кнопка для очистки корзины.
    * Кнопка "Сохранить" для отправки изменений в CRM.
  * **Стилизация**: Использует Bootstrap для оформления (`panel`, `btn`, `form-input`, `table`, `grid`).
* **Назначение**: Предоставляет интерфейс для поиска заказа, редактирования его состава и управления корзиной.

***

#### 3. **JavaScript-логика (Alpine.js)**

```javascript
<script>
    const initialData = {
        typeBoxes: <?= json_encode($typeBoxes) ?>,
        colors: <?= json_encode($colors) ?>,
        orderTypes: <?= json_encode($orderTypes) ?>,
        path: '<?= $path ?>',
        cellsType: <?= json_encode($cels) ?>
    };

    document.addEventListener('alpine:init', () => {
        Alpine.data('OrderManager', () => ({
            cart: JSON.parse(localStorage.getItem('cart_edit')) || [],
            boxTypes: initialData.typeBoxes,
            cardboardColors: initialData.colors,
            orderTypes: initialData.orderTypes,
            elements: { ... },
            cels: initialData.cellsType,

            init() { ... },
            declineDay(days) { ... },
            fetchOrder() { ... },
            calculatePrice() { ... },
            getFormParams() { ... },
            validateParams(params) { ... },
            addItem() { ... },
            updateCartTable() { ... },
            formatOrderType(orderType) { ... },
            editItem(index) { ... },
            removeItem(index) { ... },
            clearCart() { ... },
            saveItems() { ... },
            calculateDelivery() { ... },
            showOrderNotFound() { ... }
        }));
    });
</script>
```

* **Описание**:
  * **Передача данных**: PHP-данные (`typeBoxes`, `colors`, `orderTypes`, `path`, `cels`) передаются в JavaScript через `json_encode`.
  * **Компонент `OrderManager`**:
    * **Данные**:
      * `cart`: Корзина, загружаемая из `localStorage` (`cart_edit`).
      * `boxTypes`, `cardboardColors`, `orderTypes`, `cels`: Данные о типах коробок, цветах, заказах и ложементах.
      * `elements`: Ссылки на DOM-элементы формы и таблицы.
    * **Методы**:
      * `init()`: Инициализирует обработчики событий, включая поиск заказа и логику для ложементов (автоматический выбор типа 933).
      * `declineDay(days)`: Склоняет слово "день" для отображения сроков заказа.
      * `fetchOrder()`: Загружает данные заказа через `copyOrder.php`, заполняет корзину и показывает форму редактирования.
      * `calculatePrice()`: Рассчитывает цены через `calcBox.php`, обновляя таблицу цен.
      * `getFormParams()`: Собирает параметры формы (тип коробки, цвет, размеры, количество, тип заказа, ложемент).
      * `validateParams(params)`: Проверяет заполненность параметров.
      * `addItem()`: Добавляет позицию в корзину, сохраняет в `localStorage` и обновляет таблицу.
      * `updateCartTable()`: Обновляет таблицу корзины и итоговую стоимость.
      * `formatOrderType(orderType)`: Форматирует тип заказа для отображения.
      * `editItem(index)`: Заполняет форму данными позиции для редактирования.
      * `removeItem(index)`: Удаляет позицию из корзины.
      * `clearCart()`: Очищает корзину с подтверждением через SweetAlert2.
      * `saveItems()`: Отправляет обновленный состав заказа в `editOrder.php`.
      * `calculateDelivery()`: Рассчитывает стоимость доставки через `calcDelivery.php`.
      * `showOrderNotFound()`: Показывает уведомление об ошибке, если заказ не найден.
  * **Библиотеки**:
    * **Alpine.js**: Управляет состоянием и интерактивностью.
    * **jQuery**: Для AJAX-запросов и DOM.
    * **SweetAlert2**: Для модальных окон.
    * **Toastify**: Для уведомлений.
    * **Fetch API**: Для асинхронных запросов.
* **Назначение**: Обеспечивает динамическое управление составом заказа, расчет цен и доставки, а также интеграцию с CRM.

***

### 🔗 Зависимости

Файл зависит от следующих компонентов:

* **Серверные объекты**:
  * `$flags`: Объект `UserFlags` для проверки прав доступа.
  * `$read`: PDO-объект для запросов к таблицам `types_boxes` и `type_orders`.
  * `$cardboard_pdo`: PDO-объект для запросов к таблице `cardboardStore`.
  * `$prod_formulas_drawings`: PDO-объект для запросов к таблице `cells_0933`.
  * `$path`: Переменная с базовым путем приложения (из `config.php`).
  * `CSRF`: Класс для генерации CSRF-токенов (из `content/security/CSRF.php`).
* **Файлы**:
  * `content/security/flag.php`: Класс `UserFlags`.
  * `content/security/CSRF.php`: Класс `CSRF`.
  * `assets/app/php/orders/cardboard/copyOrder.php`: Получение данных заказа.
  * `assets/app/php/orders/cardboard/calcBox.php`: Расчет цен.
  * `assets/app/php/calcDelivery.php`: Расчет стоимости доставки.
  * `assets/app/php/orders/cardboard/editOrder.php`: Сохранение изменений состава заказа.
* **Библиотеки**:
  * **Bootstrap**: Для стилизации интерфейса.
  * **Alpine.js**: Для управления состоянием.
  * **jQuery**: Для AJAX-запросов и DOM.
  * **SweetAlert2**: Для уведомлений.
  * **Toastify**: Для всплывающих сообщений.
  * **Fetch API**: Для асинхронных запросов.

***

### 📑 Роль в АдминПанели

Файл `ordersCardboardEditItems.php` выполняет следующие функции в контексте АдминПанели:

* **Редактирование состава заказа**: Позволяет добавлять, изменять или удалять позиции в заказе, включая тип коробки, цвет, размеры, количество и ложемент.
* **Интеграция с CRM**: Загружает данные заказа через RetailCRM и сохраняет изменения.
* **Калькуляция цен**: Рассчитывает цены для Muul, Boxstore и Ozon в реальном времени.
* **Управление доставкой**: Отображает стоимость доставки для разных способов (самовывоз, ПВЗ, до двери).
* **Безопасность**: Использует CSRF-токены для защиты AJAX-запросов и проверяет права доступа через флаги.
* **Удобство интерфейса**: Предоставляет интерактивный интерфейс с корзиной, уведомлениями и валидацией данных.

***

### ⚙️ Технические детали

* **Сессии**: Проверяет `$_SESSION['user']` через `$flags` для контроля доступа.
* **Маршрутизация**: Вызывается через параметр `$_GET['do'] = ordersCardboardEditItems` в `router.php`.
* **Базы данных**: Использует PDO для запросов к таблицам `types_boxes`, `cardboardStore`, `type_orders`, `cells_0933`.
* **AJAX**: Использует Fetch API и jQuery для запросов к `copyOrder.php`, `calcBox.php`, `calcDelivery.php`, `editOrder.php`.
* **Хранение данных**: Использует `localStorage` (`cart_edit`) для временного хранения корзины.
* **Интерфейс**: Построен на Bootstrap с интерактивностью через Alpine.js, уведомлениями (Toastify) и модальными окнами (SweetAlert2).
* **Безопасность**: Применяет CSRF-токены и проверяет права доступа.

***
