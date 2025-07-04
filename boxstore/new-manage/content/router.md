---
icon: php
---

# router

## 📄 Документация по файлу `router.php` (АдминПанель)

Файл `router.php` является маршрутизатором веб-приложения **АдминПанель**, написанного на PHP. Он отвечает за определение и подключение соответствующего контента на основе параметра запроса `$_GET['do']`. Документация описывает структуру, функциональность и назначение файла в контексте административной панели.

***

### 🚀 Обзор

Файл `router.php` выполняет следующие задачи:

* Проверяет авторизацию пользователя и перенаправляет неавторизованных на главную страницу.
* Определяет маршрут на основе параметра `$_GET['do']`.
* Подключает соответствующий PHP-файл из директории `content/app` в зависимости от маршрута.
* Поддерживает широкий спектр маршрутов для управления складом, заказами, доставкой, производством, маркетплейсами и другими функциями АдминПанели.
* Если маршрут не указан или неизвестен, подключает страницу дашборда по умолчанию.

***

### 📋 Структура и функциональность

#### 1. **Проверка авторизации**

```php
if (!$_SESSION['user']) {
    header('Location: /');
    exit;
}
```

* **Описание**:
  * Проверяет наличие переменной `$_SESSION['user']`, которая указывает на авторизацию пользователя.
  * Если пользователь не авторизован, перенаправляет на главную страницу (`/`) с помощью заголовка `Location` и завершает выполнение скрипта (`exit`).
* **Назначение**: Обеспечивает доступ к маршрутам только для авторизованных пользователей.

***

#### 2. **Определение маршрута**

```php
$router = $_GET['do'] ?? '';
```

* **Описание**:
  * Извлекает значение параметра `$_GET['do']`, который определяет маршрут.
  * Использует оператор null coalescing (`??`) для установки пустой строки, если параметр отсутствует.
* **Назначение**: Определяет, какой контент будет отображен на основе пользовательского запроса.

***

#### 3. **Маршрутизация**

```php
switch ($router) {
    // STORE
    // CARDBOARD
    case 'storeCardboardLeftover':
        include 'content/app/store/cardboard/storeCardboardLeftover.php';
        break;
    // ... (другие маршруты)
    default:
        include 'content/app/dashboard.php';
        break;
}
```

* **Описание**:
  * Использует конструкцию `switch` для выбора PHP-файла на основе значения `$router`.
  * Каждый `case` соответствует определенному маршруту и подключает соответствующий файл из директории `content/app`.
  * Если `$router` пустой или не соответствует ни одному маршруту, подключается страница дашборда (`content/app/dashboard.php`).
* **Назначение**: Обеспечивает маршрутизацию запросов к соответствующим модулям приложения.

***

#### 4. **Список маршрутов**

Файл поддерживает множество маршрутов, сгруппированных по функциональным категориям:

**Склад (Store)**

* **Картон (Cardboard)**:
  * `storeCardboardLeftover`: Управление остатками картона.
  * `storeCardboardConsumption`: Учет расхода картона.
  * `storeCardboardHistory`: История операций с картоном (повторяется в коде, возможно, ошибка).
  * `storeCardboardSupply`: Поставки картона.
  * `storeCardboardInventory`: Инвентаризация картона.
  * `storeCardboardScraps`: Учет обрезков картона.
  * `storeCardboardPrice`: Цены на картон для производства.
  * `storeCardboardPriceClient`: Цены на картон для клиентов.
  * `storeCalendarArrivals`: Календарь поступлений картона.
* **Коробки (Box)**:
  * `storeBoxOzon`: Управление коробками для Ozon.
* **Пластик (Plastic)**:
  * `storePlasticOzon`: Управление пластиковыми изделиями для Ozon.
  * `storePlasticBoxesForScreens`: Коробки для экранов.
  * `storePlasticFastenings`: Крепления.
  * `storePlasticFasteningsHistory`: История операций с креплениями.

**Заказы (Orders)**

* **Картон (Cardboard)**:
  * `ordersCardboardAccounting`: Учет заказов на картон.
  * `ordersCardboardCreateOrder`: Создание заказа на картон.
  * `ordersCardboardCopyOrder`: Копирование заказа.
  * `ordersCardboardEditItems`: Редактирование элементов заказа.
  * `ordersCardboardEditDelivery`: Редактирование доставки.
  * `ordersCardboardCBigOzonOrders`: Заказы для Ozon (CBig).
  * `ordersCardboard933Image`: Изображения для коробок типа 933.
  * `ordersCardboardCdekMuulPVZ`: Заказы с доставкой через СДЭК (MUUL PVZ).

**Доставка (Delivery)**

* **Вспомогательные функции**:
  * `deliveryHelper`: Вспомогательные функции доставки.
* **Картон (Cardboard)**:
  * `deliveryCardboardCreateCdek`: Создание доставки через СДЭК.
  * `deliveryCardboardCdekToDoor`: Доставка СДЭК до двери.
  * `deliveryCardboardCdekMuulPVZ`: Доставка СДЭК до пункта выдачи MUUL.
* **Пластик (Plastic)**:
  * `deliveryPlasticCreateCdek`: Создание доставки пластиковых изделий через СДЭК.

**Администрирование (Admin)**

* **Калькуляция**:
  * `adminCalcPromocodes`: Управление промокодами.
  * `adminCalcParams`: Настройка параметров калькуляции.
* **Пользователи**:
  * `adminUserFlag`: Управление флагами пользователей.
* **Redis**:
  * `adminRedisAirtable`: Интеграция с Airtable через Redis.
  * `adminRedisOzon`: Интеграция с Ozon через Redis.
  * `adminRedisCdek`: Интеграция с СДЭК через Redis.
  * `adminRedisCrm`: Интеграция с CRM через Redis.

**Чертежи (Drawings)**

* `drawings`: Общий интерфейс для работы с чертежами.
* **Настройки чертежей**:
  * `drawingsSettingbox6Gran`: Настройки для коробок типа 6-Gran.
  * `drawingsSettingbox1121`: Настройки для коробок типа 1121.
  * `drawingsSettingbox0110`: Настройки для коробок типа 0110.
  * `drawingsSettingbox0201`: Настройки для коробок типа 0201.
  * `drawingsSettingbox0215`: Настройки для коробок типа 0215.
  * `drawingsSettingbox0426`: Настройки для коробок типа 0426.
  * `drawingsSettingbox0427`: Настройки для коробок типа 0427.
  * `drawingsSettingbox0300`: Настройки для коробок типа 0300.
  * `drawingsSettingbox0330`: Настройки для коробок типа 0330.
  * `drawingsSettingbox0933`: Настройки для коробок типа 0933.

**Производство (Production)**

* **Персонал (Staff)**:
  * `productionStaffStats`: Статистика персонала.
  * `productionStaffGenerateDrawings`: Генерация чертежей персоналом.
  * `productionStaffCreateAct`: Создание акта персоналом.
  * `productionStaffLog`: Логи персонала.
* **Мейкеры (Makers)**:
  * `productionMakersReadyPackaginCardboard`: Готовые упаковки из картона.
  * `productionMakersSendOrderToReadyCardboard`: Отправка заказа в готовые картонные упаковки.
  * `productionMakerGenerateDrawings`: Генерация чертежей мейкерами (совпадает с `drawings`).
  * `productionMakerGetWbBarcodes`: Получение штрихкодов Wildberries.
  * `productionMakerShiftMachine`: Смена оборудования мейкеров.
* **Упаковка**:
  * `productionPackaging`: Производство упаковки.
  * `productionPackagingLog`: Логи производства упаковки.
* **Распределение заказов**:
  * `productionDistributionOrder`: Распределение заказов.
* **Смены персонала**:
  * `productionMakerShiftstaffMain`: Основной интерфейс смен персонала.
  * `productionMakerShiftstaffPlannedload`: Планируемая загрузка персонала.

**Тестирование (Test)**

* **Andrey**:
  * `testAndreyTest1`, `testAndreyTest2`, `testAndreyTest3`: Тестовые модули Андрея.
* **Mikhail**:
  * `testMikhailTest1`, `testMikhailTest2`, `testMikhailTest3`, `testMikhailTest4`: Тестовые модули Михаила.
* **Stepan**:
  * `testStepanTest1`, `testStepanTest2`, `testStepanTest3`: Тестовые модули Степана.
* **Andrey B**:
  * `testAndreyBTest1`, `testAndreyBTest2`, `testAndreyBTest3`: Тестовые модули Андрея Б.

**Маркетплейсы (Marketplaces)**

* **Ozon**:
  * `marketplacesOzonFbo`: Управление заказами FBO Ozon.
  * `marketplacesOzonUploadItems`: Загрузка товаров на Ozon.
  * `marketplacesOzonEditItems`: Редактирование товаров на Ozon.
* **Wildberries**:
  * `marketplacesWildberriesFbo`: Управление заказами FBO Wildberries.
  * `marketplacesWildberriesUploadItems`: Загрузка товаров на Wildberries.
  * `marketplacesWildberriesEditItems`: Редактирование товаров на Wildberries.
  * `marketplacesWildberriesEditMedia`: Редактирование медиафайлов на Wildberries.
* **Yandex**:
  * `marketplacesYandexFbo`: Управление заказами FBO Яндекс.
  * `marketplacesYandexUploadItems`: Загрузка товаров на Яндекс.
  * `marketplacesYandexEditItems`: Редактирование товаров на Яндекс.

**Платформа (Platform)**

* `platformOrder`: Управление отдельным заказом на платформе.
* `platformOrders`: Список заказов на платформе.
* `platformUsers`: Управление пользователями платформы.
* `platformMetriks`: Метрики платформы.
* `platformCarts`: Управление корзинами на платформе.

**Пользователи (User)**

* `users`: Список пользователей.
* `createUser`: Создание пользователя.
* `editUser`: Редактирование пользователя.
* `flags`: Управление флагами пользователей.

**Другие маршруты**

* `createOrder`: Создание заказа.
* `copyOrder`: Копирование заказа.
* `editOrderLocation`: Редактирование местоположения заказа.
* `editOrderStructure`: Редактирование структуры заказа.
* `cardboardStoreSupply`: Поставки картона на склад.
* `cardboardStore`: Управление складом картона.
* `cardboardStoreConsumption`: Учет расхода картона.
* `calcParams`: Настройка параметров калькуляции.
* `priceCardboardClient`: Цены на картон для клиентов.
* **По умолчанию**: `dashboard.php` — главная страница панели управления.

***

### 🔗 Зависимости

Файл зависит от следующих компонентов:

* **Сессии**: `$_SESSION['user']` для проверки авторизации.
* **Входные параметры**: `$_GET['do']` для определения маршрута.
* **Файлы контента**: Все указанные PHP-файлы в директории `content/app`, соответствующие маршрутам (например, `content/app/store/cardboard/storeCardboardLeftover.php`, `content/app/dashboard.php`).

***

### 📑 Роль в АдминПанели

Файл `router.php` выполняет следующие функции в контексте АдминПанели:

* **Маршрутизация**: Определяет, какой модуль или страница будет отображена на основе параметра `$_GET['do']`.
* **Контроль доступа**: Ограничивает доступ к маршрутам только для авторизованных пользователей.
* **Модульность**: Поддерживает подключение различных функциональных модулей, разделенных по категориям (склад, заказы, доставка, производство, маркетплейсы и т.д.).
* **Интеграция**: Связывает пользовательский интерфейс с серверной логикой, обеспечивая доступ к управлению складом, заказами, производством и интеграциями с внешними сервисами (Ozon, Wildberries, СДЭК, CRM).

***

### ⚙️ Технические детали

* **Сессии**: Проверяет `$_SESSION['user']` для контроля доступа.
* **Маршрутизация**: Использует `switch` для обработки множества маршрутов, основанных на `$_GET['do']`.
* **Файловая структура**: Ожидает наличие PHP-файлов в поддиректориях `content/app`, организованных по функциональным категориям.
* **Обработка по умолчанию**: Подключает `dashboard.php`, если маршрут не указан или неизвестен.
