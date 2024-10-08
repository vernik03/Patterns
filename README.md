**Звіт про виконання лабораторної роботи №1**

**Тема:** Патерн проектування Одинак (Singleton)

**Мета:** Здобути навички з реалізацією патерну проектування Одинак.

---

**Хід роботи:**

1. **Реалізація системи управління файлами користувача**

   Для реалізації системи управління файлами користувача з використанням патерну Одинак необхідно створити клас, який гарантує існування лише одного екземпляра для кожного типу сховища. Це дозволить централізовано керувати підключеннями до різних сховищ та забезпечить розширюваність системи в майбутньому.

2. **Структура класів та методів**

   **Клас `StorageManager` (Одинак)**

   ```cpp
   class StorageManager {
   private:
       static StorageManager* instance;
       StorageManager(); // Приватний конструктор
       std::map<std::string, Storage*> storages; // Список сховищ
   public:
       static StorageManager* getInstance();
       void addStorage(const std::string& userID, Storage* storage);
       Storage* getStorage(const std::string& userID);
   };
   ```

   - **Методи:**
     - `static StorageManager* getInstance();`
       - **Параметри:** Немає
       - **Повертає:** Єдиний екземпляр класу `StorageManager`
     - `void addStorage(const std::string& userID, Storage* storage);`
       - **Параметри:** `userID` - ідентифікатор користувача, `storage` - вказівник на обране сховище
       - **Повертає:** Нічого
     - `Storage* getStorage(const std::string& userID);`
       - **Параметри:** `userID` - ідентифікатор користувача
       - **Повертає:** Вказівник на сховище користувача

   **Абстрактний клас `Storage`**

   ```cpp
   class Storage {
   public:
       virtual void connect() = 0;
       virtual void uploadFile(const std::string& filePath) = 0;
       virtual void downloadFile(const std::string& fileName) = 0;
       virtual void deleteFile(const std::string& fileName) = 0;
       virtual ~Storage() {}
   };
   ```

   - **Методи:**
     - `void connect();`
       - **Параметри:** Немає
       - **Повертає:** Нічого
     - `void uploadFile(const std::string& filePath);`
       - **Параметри:** `filePath` - шлях до файлу для завантаження
       - **Повертає:** Нічого
     - `void downloadFile(const std::string& fileName);`
       - **Параметри:** `fileName` - ім'я файлу для завантаження
       - **Повертає:** Нічого
     - `void deleteFile(const std::string& fileName);`
       - **Параметри:** `fileName` - ім'я файлу для видалення
       - **Повертає:** Нічого

   **Клас `LocalStorage`**

   ```cpp
   class LocalStorage : public Storage {
   public:
       void connect() override;
       void uploadFile(const std::string& filePath) override;
       void downloadFile(const std::string& fileName) override;
       void deleteFile(const std::string& fileName) override;
   };
   ```

   - **Методи:** Реалізують відповідні функції для локального диска.

   **Клас `AmazonS3Storage`**

   ```cpp
   class AmazonS3Storage : public Storage {
   public:
       void connect() override;
       void uploadFile(const std::string& filePath) override;
       void downloadFile(const std::string& fileName) override;
       void deleteFile(const std::string& fileName) override;
   };
   ```

   - **Методи:** Реалізують відповідні функції для Amazon S3.

3. **Розширюваність системи**

   Завдяки використанню абстрактного класу `Storage`, додавання нових типів сховищ у майбутньому буде простим. Достатньо створити новий клас, який успадковує `Storage` та реалізує його методи.

4. **Налаштування сховища для кожного користувача окремо**

   Клас `StorageManager` зберігає відповідність між користувачами та їх обраними сховищами в `std::map<std::string, Storage*> storages;`.

   **Приклад використання:**

   ```cpp
   // Отримуємо екземпляр менеджера сховищ
   StorageManager* manager = StorageManager::getInstance();

   // Створюємо сховище для користувача
   Storage* userStorage = new LocalStorage();
   userStorage->connect();

   // Додаємо сховище для користувача з ID "user123"
   manager->addStorage("user123", userStorage);

   // Завантажуємо файл
   manager->getStorage("user123")->uploadFile("path/to/file.txt");
   ```

---

**Висновок:**

У ході виконання лабораторної роботи було розроблено структуру класів та методів для системи управління файлами користувача з використанням патерну проектування Одинак. Створена структура дозволяє користувачам підключатися до різних сховищ, обраних індивідуально, та забезпечує можливість розширення списку доступних сховищ у майбутньому.