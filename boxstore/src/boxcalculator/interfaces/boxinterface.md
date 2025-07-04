---
icon: php
---

# BoxInterface

## 📦 Документация по интерфейсу BoxInterface

Добро пожаловать в документацию по интерфейсу `BoxInterface`! 🎉 Этот PHP-интерфейс, расположенный в пространстве имен `BoxCalculator\Interfaces`, определяет контракт для классов, которые инициализируют подключения к базам данных в системе калькуляции коробок. Интерфейс включает один метод для настройки соединений с базами данных, обеспечивая единообразный подход к управлению доступом к данным. Ниже вы найдете подробное описание метода интерфейса, его назначение и рекомендации по реализации.

***

### 🚀 Обзор

Интерфейс `BoxInterface` разработан для обеспечения единообразного механизма инициализации подключений к базам данных, используемым в системе калькуляции коробок. Он предназначен для реализации в классах, таких как `Box`, которые:

* Настраивают подключения к базам данных (`cardboard_store`, `cloud_muul`, `muul_formulas_drawings`) через PDO 📡
* Обеспечивают обработку ошибок подключения через исключения 🔧
* Служат основой для дальнейших операций с коробками, таких как расчет параметров и стоимости 📦

Интерфейс гарантирует, что любой класс, реализующий его, предоставляет метод для корректной инициализации подключений, что упрощает интеграцию и поддержку кода.

***

### 🛠️ Установка и настройка

Чтобы использовать интерфейс `BoxInterface`, выполните следующие шаги:

1.  **Подключите интерфейс**: Убедитесь, что файл интерфейса включен в ваш PHP-проект.

    ```php
    require_once 'path/to/BoxInterface.php';
    ```
2.  **Создайте реализующий класс**: Реализуйте интерфейс в классе, например, `Box`, который будет управлять подключениями к базам данных.

    ```php
    namespace BoxCalculator;

    use BoxCalculator\Interfaces\BoxInterface;
    use PDO;
    use Exception;

    class Box implements BoxInterface {
        private array $config;
        private PDO $pdoCardboardStore;
        private PDO $pdoCloudMuul;
        private PDO $pdoDrawings;

        public function __construct(array $config) {
            $this->config = $config;
            $this->initializeConnections($config);
        }

        public function initializeConnections(array $config): void {
            // Реализация метода (см. ниже)
        }
    }
    ```
3.  **Настройте конфигурацию**: Подготовьте массив конфигурации для баз данных.

    ```php
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
    ```
4.  **Создайте экземпляр класса**: Передайте конфигурацию в конструктор реализующего класса.

    ```php
    try {
        $box = new Box($config);
        echo 'Подключения успешно инициализированы';
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```
5. **Используйте класс**: Реализующий класс готов к дальнейшим операциям с коробками.

***

### 📋 Метод интерфейса

Интерфейс `BoxInterface` определяет один метод, который должен быть реализован в классах. Ниже описаны его назначение, параметры, возвращаемое значение и рекомендации по реализации.

#### `initializeConnections(array $config): void` 🔧

Инициализирует подключения к базам данных.

* **Параметры**:
  * `$config` (array): Массив конфигурации, содержащий данные для подключения к базам данных (`cardboard_store`, `cloud_muul`, `muul_formulas_drawings`).
* **Возвращает**: `void`
* **Исключения**: Должен выбрасывать `\Exception`, если подключение к любой базе данных не удалось.
* **Рекомендации по реализации**:
  * Проверьте наличие необходимых ключей в `$config` (например, `databases.cardboard_store`, `databases.cloud_muul`, `databases.muul_formulas_drawings`).
  * Для каждой базы данных извлеките параметры подключения (`host`, `database`, `users.read.username`, `users.read.password`).
  * Создайте объекты PDO для каждой базы данных, используя DSN в формате `mysql:host=$host;dbname=$database`.
  *   Установите атрибуты PDO для строгой обработки ошибок, например:

      ```php
      $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
      ```
  * Сохраните PDO-объекты в свойствах класса для дальнейшего использования.
  * Выбрасывайте исключение с описательной ошибкой, если подключение не удалось.
  *   Пример реализации:

      ```php
      public function initializeConnections(array $config): void {
          if (!isset($config['databases']['cardboard_store'], $config['databases']['cloud_muul'], $config['databases']['muul_formulas_drawings'])) {
              throw new Exception('Database configuration is incomplete.');
          }

          try {
              $cardboardDb = $config['databases']['cardboard_store'];
              $this->pdoCardboardStore = new PDO(
                  "mysql:host={$cardboardDb['host']};dbname={$cardboardDb['database']};charset=utf8mb4",
                  $cardboardDb['users']['read']['username'],
                  $cardboardDb['users']['read']['password'],
                  [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]
              );

              $cloudDb = $config['databases']['cloud_muul'];
              $this->pdoCloudMuul = new PDO(
                  "mysql:host={$cloudDb['host']};dbname={$cloudDb['database']};charset=utf8mb4",
                  $cloudDb['users']['read']['username'],
                  $cloudDb['users']['read']['password'],
                  [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]
              );

              $drawingsDb = $config['databases']['muul_formulas_drawings'];
              $this->pdoDrawings = new PDO(
                  "mysql:host={$drawingsDb['host']};dbname={$drawingsDb['database']};charset=utf8mb4",
                  $drawingsDb['users']['read']['username'],
                  $drawingsDb['users']['read']['password'],
                  [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]
              );
          } catch (Exception $e) {
              throw new Exception('Failed to connect to databases: ' . $e->getMessage());
          }
      }
      ```
*   **Пример использования**:

    ```php
    try {
        $box->initializeConnections($config);
        echo 'Подключения инициализированы';
    } catch (Exception $e) {
        echo 'Ошибка: ' . $e->getMessage();
    }
    ```

***

### ⚠️ Лучшие практики

*   **Обработка ошибок**: Реализуйте надежную обработку исключений, чтобы информировать о проблемах с конфигурацией или подключением.

    ```php
    if (!isset($config['databases']['cardboard_store']['host'])) {
        throw new Exception('Missing host for cardboard_store database.');
    }
    ```
* **Валидация конфигурации**:
  * Проверяйте наличие всех необходимых ключей в `$config` перед созданием PDO-объектов.
  * Убедитесь, что параметры подключения (`host`, `database`, `username`, `password`) не пустые.
* **Безопасность**:
  * Храните учетные данные (`username`, `password`) в безопасном месте (например, переменных окружения).
  * Используйте кодировку `utf8mb4` в DSN для поддержки Unicode.
* **Оптимизация**:
  * Настройте PDO-атрибуты для оптимальной работы (например, `PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION` для строгой обработки ошибок).
  * Рассмотрите использование пула соединений для масштабируемых приложений.
*   **Логирование**: Внедрите логирование ошибок подключения для упрощения отладки.

    ```php
    catch (Exception $e) {
        error_log('Database connection error: ' . $e->getMessage());
        throw $e;
    }
    ```
* **Расширяемость**: Реализуйте метод так, чтобы он легко адаптировался к добавлению новых баз данных в будущем.

***

### 🐛 Устранение неполадок

* **Ошибка конфигурации**:
  * Проверьте, что `$config` содержит все необходимые ключи (`databases.cardboard_store`, `databases.cloud_muul`, `databases.muul_formulas_drawings`).
  * Убедитесь, что подключи `host`, `database`, `users.read.username`, `users.read.password` корректны.
* **Ошибка подключения**:
  * Проверьте доступность MySQL-сервера и правильность учетных данных.
  * Убедитесь, что пользователь PDO имеет права на чтение для указанных баз данных.
  * Проверьте, что DSN сформирован корректно (например, `mysql:host=localhost;dbname=cardboard_store`).
* **PDO исключения**:
  * Включите `PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION` для получения подробных сообщений об ошибках.
  * Проверьте сетевые настройки, если сервер базы данных находится на удаленном хосте.

***
