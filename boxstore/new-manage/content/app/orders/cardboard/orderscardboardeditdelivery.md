---
icon: php
---

# ordersCardboardEditDelivery

## 📄 Документация по файлу `ordersCardboardEditDelivery.php` (АдминПанель)

Файл `ordersCardboardEditDelivery.php` является модулем веб-приложения **АдминПанель**, расположенным в директории `content/app/orders/cardboard`. Он отвечает за редактирование логистики существующего заказа на картонные коробки, включая изменение способа доставки, адреса и даты отгрузки. Файл сочетает серверную логику на PHP с клиентским интерфейсом, использующим HTML, CSS, JavaScript, Alpine.js, Bootstrap, jQuery, SweetAlert2 и виджет СДЭК. Документация описывает структуру, функциональность и назначение файла в контексте административной панели.

***

### 🚀 Обзор

Файл `ordersCardboardEditDelivery.php` выполняет следующие задачи:

* Проверяет права доступа пользователя через флаг `ORDERS_CARDBOARD_EDITDELIVERY`.
* Отображает интерфейс для поиска заказа по номеру и редактирования его логистики.
* Позволяет изменить дату отгрузки в Airtable.
* Поддерживает выбор способа доставки (самовывоз, ПВЗ, до двери) с интеграцией виджета СДЭК для выбора ПВЗ и сервиса Dadata для автодополнения адресов.
* Рассчитывает стоимость доставки через AJAX-запросы.
* Сохраняет изменения логистики через AJAX-запросы в CRM.
* Предоставляет возможность принудительного создания доставки через СДЭК.
* Использует CSRF-токены для защиты запросов.

***

### 📋 Структура и функциональность

#### 1. **Проверка прав доступа**

```php
<?php if ($flags->hasAnyFlag(['ORDERS_CARDBOARD_EDITDELIVERY'])) { ?>
```

* **Описание**:
  * Проверяет наличие флага `ORDERS_CARDBOARD_EDITDELIVERY` у пользователя с помощью объекта `$flags` и метода `hasAnyFlag`.
  * Если флаг отсутствует, содержимое файла не отображается, ограничивая доступ к функционалу.
* **Назначение**: Обеспечивает контроль доступа, позволяя только пользователям с соответствующими правами редактировать логистику заказов.

***

#### 2. **Подключение скриптов и стилей**

```php
<script src="https://cdn.jsdelivr.net/npm/@cdek-it/widget@3" type="text/javascript"></script>
<style>
    .suggestions-list { list-style: none; padding: 0; margin: 0; background: #fff; }
    .suggestions-list li { padding: 5px 10px; cursor: pointer; }
    .suggestions-list li:hover { background: #f0f0f0; }
    :is(.dark .suggestions-list) { background: #060818; border: 1px solid #121e31; }
    :is(.dark .suggestions-list li) { color: #fff; }
    :is(.dark .suggestions-list li:hover) { background: #121e31; }
</style>
```

* **Описание**:
  * **Скрипты**:
    * Подключает виджет СДЭК версии 3 (`@cdek-it/widget@3`) для выбора пункта выдачи (ПВЗ).
  * **Стили**:
    * Определяет стили для списка автодополнения адресов (`.suggestions-list`), включая оформление элементов и ховер-эффект.
    * Поддерживает темную тему с помощью селектора `:is(.dark .suggestions-list)`, изменяя цвета фона и текста.
* **Назначение**: Подготавливает клиентские ресурсы для интерактивного интерфейса и автодополнения адресов.

***

#### 3. **Клиентский интерфейс**

```html
<div x-data="ordersCardboardEditDelivery">
    <input type="hidden" name="_csrf" value="<?= CSRF::generateToken() ?>">
    <ul class="flex space-x-2 rtl:space-x-reverse"> ... </ul>
    <h2 class="text-2xl font-semibold mt-5 mb-5">Редактирование логистики</h2>

    <div class="panel mt-5">
        <div class="mb-5 flex items-center">
            <input id="orderNumber" type="text" class="form-input ltr:rounded-r-none rtl:rounded-l-none" placeholder="Введите номер заказа">
            <button id="findOrderBtn" class="btn btn-secondary ltr:rounded-l-none rtl:rounded-r-none">Найти</button>
        </div>
    </div>

    <div id="orderInfoContainer"></div>

    <div class="panel mt-5" id="orderInfoEdit" style="display: none;">
        <h5 class="text-lg font-semibold mb-5 dark:text-white-light">Логистика</h5>
        <form id="dateForm" class="mb-5"> ... </form>
        <form id="logisticForm"> ... </form>
    </div>
</div>
```

* **Описание**:
  * **Компонент Alpine.js**: Использует компонент `ordersCardboardEditDelivery` для управления состоянием.
  * **CSRF-токен**: Включает скрытое поле с CSRF-токеном, сгенерированным через `CSRF::generateToken()`.
  * **Хлебные крошки**: Навигационная панель с ссылкой на раздел "Заказы" и текущей страницей "Редактирование логистики".
  * **Поиск заказа**:
    * Поле ввода (`orderNumber`) для номера заказа и кнопка "Найти".
  * **Контейнер информации о заказе** (`order.ConcurrentModificationException: Concurrent modification detected in XSharedMap@3d7c02fb ConcurrentModificationException: Concurrent modification detected in XSharedMap@3d7c02fb InfoContainer`):
    * Пустой контейнер, заполняемый данными заказа через JavaScript.
  * **Форма редактирования логистики** (`orderInfoEdit`, скрыта по умолчанию):
    * **Форма изменения даты отгрузки** (`dateForm`): Поле для выбора даты и кнопка для отправки в Airtable.
    * **Форма логистики** (`logisticForm`):
      * Выбор способа доставки (`deliveryType`: самовывоз, ПВЗ, до двери).
      * Поле для адреса самовывоза (фиксированное, только чтение).
      * Интеграция с виджетом СДЭК для выбора ПВЗ (`cdek-pvz`), отображающая код ПВЗ, адрес и тариф.
      * Поле для ввода адреса доставки до двери (`cdek-do-dveri`) с автодополнением через Dadata.
      * Отображение стоимости доставки (`cost-delivery`) и скрытых полей для кода тарифа и города.
      * Кнопки "Сохранить изменения" и "ПРИНУДИТЕЛЬНО СОЗДАТЬ CDEK".
  * **Стилизация**: Использует Bootstrap для оформления (`panel`, `btn`, `form-input`, `grid`, `table`).
* **Назначение**: Обеспечивает интерфейс для поиска заказа и редактирования его логистики, включая способ доставки, адрес и дату отгрузки.

***

#### 4. **JavaScript-логика**

```javascript
<script>
    let ITEMS = {}, CLIENT = {}, LEGAL = {}, DELIVERY = {};

    document.getElementById('findOrderBtn').addEventListener('click', () => { ... });
    document.getElementById('dateForm').addEventListener('submit', (event) => { ... });
    document.getElementById('deliveryType').addEventListener('change', function () { ... });
    document.getElementById('address-door').addEventListener('input', function () { ... });

    function fetchOrderInfo(orderNumber) { ... }
    function updateOrderInfo(orderInfo, clientInfo, contragent, delivery, itemsO) { ... }
    function showOrderNotFound() { ... }
    function saveDeliveryData(dataToSend) { ... }
    window.createCdekForcedly = function () { ... };
</script>
```

* **Описание**:
  * **Глобальные переменные**:
    * `ITEMS`, `CLIENT`, `LEGAL`, `DELIVERY`: Объекты для хранения данных заказа, клиента, юридической информации и доставки.
  * **Обработчики событий**:
    * **Кнопка поиска** (`findOrderBtn`): Вызывает `fetchOrderInfo` для загрузки данных заказа по номеру.
    * **Форма даты отгрузки** (`dateForm`): Отправляет запрос к `changeShipmentDate.php` для обновления даты в Airtable.
    * **Выбор способа доставки** (`deliveryType`): Показывает/скрывает соответствующие поля (самовывоз, ПВЗ, до двери), инициализирует виджет СДЭК для ПВЗ, запрашивает стоимость доставки через `getDeliveryPrice.php`.
    * **Ввод адреса до двери** (`address-door`): Запрашивает автодополнение адресов через `suggestions.php` (Dadata) и обновляет стоимость доставки.
  * **Функции**:
    * `fetchOrderInfo(orderNumber)`: Отправляет AJAX-запрос к `copyOrder.php` для получения данных заказа, заполняет `orderInfoContainer` и показывает форму логистики.
    * `updateOrderInfo(orderInfo, clientInfo, contragent, delivery, itemsO)`: Обновляет интерфейс данными заказа, устанавливая значения полей доставки и стоимости.
    * `showOrderNotFound()`: Отображает сообщение "Заказ не найден" в `orderInfoContainer`.
    * `saveDeliveryData(dataToSend)`: Отправляет AJAX-запрос к `editDelivery.php` для сохранения изменений логистики, отображает уведомления через SweetAlert2.
    * `createCdekForcedly()`: Принудительно создает доставку через СДЭК, отправляя запросы к внешним скриптам (`createCdekForcedly.php`, `addTrekkerInAirtable.php`).
  * **Библиотеки**:
    * **jQuery**: Для AJAX-запросов и манипуляций с DOM.
    * **SweetAlert2**: Для отображения уведомлений и ошибок.
    * **Fetch API**: Для асинхронных запросов.
    * **CDEK Widget**: Для выбора ПВЗ.
    * **Alpine.js**: Для управления состоянием компонента `ordersCardboardEditDelivery`.
* **Назначение**: Обеспечивает динамическую загрузку данных заказа, интерактивное редактирование логистики и интеграцию с внешними сервисами.

***

### 🔗 Зависимости

Файл зависит от следующих компонентов:

* **Серверные объекты**:
  * `$flags`: Объект `UserFlags` для проверки прав доступа (`hasAnyFlag`).
  * `$path`: Переменная с базовым путем приложения (из `config.php`).
  * `CSRF`: Класс для генерации и проверки CSRF-токенов (из `content/security/CSRF.php`).
* **Файлы**:
  * `assets/app/php/orders/cardboard/copyOrder.php`: Получение данных заказа по номеру.
  * `assets/app/php/orders/cardboard/getDeliveryPrice.php`: Расчет стоимости доставки.
  * `assets/app/php/orders/cardboard/editDelivery.php`: Сохранение изменений логистики.
  * `assets/app/php/orders/cardboard/changeShipmentDate.php`: Обновление даты отгрузки в Airtable.
  * `assets/app/php/dadata/suggestions.php`: Автодополнение адресов через Dadata.
  * `https://cloud.muul.ru/cdek-scripts/muul/createCdekForcedly.php`: Принудительное создание доставки через СДЭК.
  * `https://cloud.muul.ru/cdek-scripts/muul/addTrekkerInAirtable.php`: Добавление трекера в Airtable.
* **Библиотеки**:
  * **Bootstrap**: Для стилизации интерфейса (`panel`, `btn`, `form-input`, `grid`).
  * **Alpine.js**: Для управления состоянием.
  * **jQuery**: Для AJAX-запросов и DOM.
  * **SweetAlert2**: Для уведомлений.
  * **CDEK Widget**: Для выбора ПВЗ.
  * **Fetch API**: Для асинхронных запросов.

***

### 📑 Роль в АдминПанели

Файл `ordersCardboardEditDelivery.php` выполняет следующие функции в контексте АдминПанели:

* **Редактирование логистики**: Позволяет изменить способ доставки, адрес и дату отгрузки для существующего заказа.
* **Интеграция с СДЭК**: Поддерживает выбор ПВЗ через виджет СДЭК и принудительное создание доставки.
* **Автодополнение адресов**: Использует Dadata для удобного ввода адресов доставки до двери.
* **Интеграция с Airtable**: Обновляет дату отгрузки заказа.
* **Интеграция с CRM**: Сохраняет изменения логистики в RetailCRM через `editDelivery.php`.
* **Безопасность**: Использует CSRF-токены для защиты AJAX-запросов и проверяет права доступа через флаги.
* **Удобство интерфейса**: Предоставляет интерактивный интерфейс с динамическим обновлением стоимости доставки и уведомлениями.

***

### ⚙️ Технические детали

* **Сессии**: Проверяет `$_SESSION['user']` через `$flags` для контроля доступа.
* **Маршрутизация**: Вызывается через параметр `$_GET['do'] = ordersCardboardEditDelivery` в `router.php`.
* **AJAX**: Использует Fetch API и jQuery для асинхронных запросов к скриптам в `assets/app/php` и внешним сервисам.
* **Интерфейс**: Построен на Bootstrap с интерактивностью через Alpine.js и уведомлениями через SweetAlert2.
* **Интеграции**: RetailCRM, СДЭК, Dadata, Airtable.
* **Безопасность**: Применяет CSRF-токены и проверяет права доступа.

***
