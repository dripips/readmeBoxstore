# Table of contents

* [📚 Вступление](README.md)

## 📦 BoxStore

* [Модули (src)](boxstore/src/README.md)
  * [AirTable](boxstore/src/airtable.md)
  * [BoxCalculator](boxstore/src/boxcalculator/README.md)
    * [Interfaces](boxstore/src/boxcalculator/interfaces/README.md)
      * [BoxCalculationInterface](boxstore/src/boxcalculator/interfaces/boxcalculationinterface.md)
      * [BoxInterface](boxstore/src/boxcalculator/interfaces/boxinterface.md)
      * [OtherInterface](boxstore/src/boxcalculator/interfaces/otherinterface.md)
      * [DeliveryCalculationInterface](boxstore/src/boxcalculator/interfaces/deliverycalculationinterface.md)
    * [Box](boxstore/src/boxcalculator/box.md)
    * [BoxCalculator](boxstore/src/boxcalculator/boxcalculator.md)
    * [DeliveryCalculation](boxstore/src/boxcalculator/deliverycalculation.md)
    * [Other](boxstore/src/boxcalculator/other.md)
  * [CardboardManager](boxstore/src/cardboardmanager/README.md)
    * [CardboardManager](boxstore/src/cardboardmanager/cardboardmanager.md)
  * [Cdek](boxstore/src/cdek/README.md)
    * [Cdek](boxstore/src/cdek/cdek.md)
    * [CdekDoor](boxstore/src/cdek/cdekdoor.md)
    * [CdekHelper](boxstore/src/cdek/cdekhelper.md)
    * [CdekPvz](boxstore/src/cdek/cdekpvz.md)
  * [CheckBox](boxstore/src/checkbox/README.md)
    * [CheckBox](boxstore/src/checkbox/checkbox.md)
  * [ClickHouse](boxstore/src/clickhouse/README.md)
    * [CalculationLogRepository](boxstore/src/clickhouse/calculationlogrepository.md)
    * [CdekLogRepository](boxstore/src/clickhouse/cdeklogrepository.md)
    * [BarcodesRepository](boxstore/src/clickhouse/barcodesrepository.md)
    * [BoxStoreRepository](boxstore/src/clickhouse/boxstorerepository.md)
  * [Informator](boxstore/src/informator/README.md)
    * [Informator](boxstore/src/informator/informator.md)
  * [Nextcloud](boxstore/src/nextcloud/README.md)
    * [Nextcloud](boxstore/src/nextcloud/nextcloud.md)
  * [Nocodb](boxstore/src/nocodb/README.md)
    * [Nocodb](boxstore/src/nocodb/nocodb.md)
  * [Ozon](boxstore/src/ozon/README.md)
    * [Ozon](boxstore/src/ozon/ozon.md)
    * [OzonCardboard](boxstore/src/ozon/ozoncardboard.md)
  * [PlasticStore](boxstore/src/plasticstore/README.md)
    * [PlasticStore](boxstore/src/plasticstore/plasticstore.md)
  * [RCRM (RetailCRM)](boxstore/src/rcrm/README.md)
    * [Traits](boxstore/src/rcrm/traits/README.md)
      * [RequestHandler](boxstore/src/rcrm/traits/requesthandler.md)
    * [CostManager](boxstore/src/rcrm/costmanager.md)
    * [CustomerManager](boxstore/src/rcrm/customermanager.md)
    * [OrderManager](boxstore/src/rcrm/ordermanager.md)
    * [RCRM](boxstore/src/rcrm/rcrm.md)
  * [Request](boxstore/src/request/README.md)
    * [Request](boxstore/src/request/request.md)
  * [Wildberries](boxstore/src/wildberries/README.md)
    * [Wildberries](boxstore/src/wildberries/wildberries.md)
  * [Yandex](boxstore/src/yandex/README.md)
    * [Yandex](boxstore/src/yandex/yandex.md)
* [АдминПанель (new-manage)](boxstore/new-manage/README.md)
  * [assets](boxstore/new-manage/assets/README.md)
    * [app](boxstore/new-manage/assets/app/README.md)
      * [php](boxstore/new-manage/assets/app/php.md)
  * [content](boxstore/new-manage/content/README.md)
    * [app](boxstore/new-manage/content/app/README.md)
      * [orders](boxstore/new-manage/content/app/orders/README.md)
        * [cardboard](boxstore/new-manage/content/app/orders/cardboard/README.md)
          * [ordersCardboardCopyOrder](boxstore/new-manage/content/app/orders/cardboard/orderscardboardcopyorder.md)
          * [ordersCardboardCreateOrder](boxstore/new-manage/content/app/orders/cardboard/orderscardboardcreateorder.md)
          * [ordersCardboardEditDelivery](boxstore/new-manage/content/app/orders/cardboard/orderscardboardeditdelivery.md)
          * [ordersCardboardEditItems](boxstore/new-manage/content/app/orders/cardboard/orderscardboardedititems.md)
    * [auth](boxstore/new-manage/content/auth.md)
    * [security](boxstore/new-manage/content/security/README.md)
      * [flag](boxstore/new-manage/content/security/flag.md)
      * [CSRF](boxstore/new-manage/content/security/csrf.md)
      * [security](boxstore/new-manage/content/security/security.md)
    * [template](boxstore/new-manage/content/template.md)
    * [router](boxstore/new-manage/content/router.md)
  * [index](boxstore/new-manage/index.md)

## 💳 Platform

* [Page 2](platform/page-2.md)

## 💼 DataBase

* [Page 1](database/page-1.md)

## 📤 API

* ```yaml
  type: builtin:openapi
  props:
    models: true
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: boxstore-api
  ```
