**Звіт про виконання лабораторної роботи №3**

**Тема:** Патерн проектування Будівельник (Builder)

**Мета:** Здобути навички з реалізацією патерну проектування Будівельник.

---

**Хід роботи:**

1. **Реалізація системи власного QueryBuilder для взаємодії з декількома СУБД**

   Для створення системи, яка буде генерувати SQL-запити для різних СУБД (PostgreSQL та MySQL), використаємо патерн проектування **Будівельник**. Це дозволить нам будувати складні SQL-запити поетапно, використовуючи спільний інтерфейс, але з можливістю специфічної реалізації для кожної СУБД.

2. **Структура класів та методів**

   **Інтерфейс `IQueryBuilder`**

   ```cpp
   class IQueryBuilder {
   public:
       virtual IQueryBuilder& select(const std::string& fields) = 0;
       virtual IQueryBuilder& where(const std::string& condition) = 0;
       virtual IQueryBuilder& limit(int limit) = 0;
       virtual std::string getSQL() const = 0;
       virtual ~IQueryBuilder() {}
   };
   ```

   - **Методи:**
     - `IQueryBuilder& select(const std::string& fields);`
       - **Параметри:** `fields` - список полів для вибору (наприклад, `"id, name, age"`)
       - **Повертає:** Посилання на об'єкт `IQueryBuilder` для ланцюжкового виклику методів
     - `IQueryBuilder& where(const std::string& condition);`
       - **Параметри:** `condition` - умова фільтрації (наприклад, `"age > 18"`)
       - **Повертає:** Посилання на об'єкт `IQueryBuilder`
     - `IQueryBuilder& limit(int limit);`
       - **Параметри:** `limit` - кількість записів для обмеження результату
       - **Повертає:** Посилання на об'єкт `IQueryBuilder`
     - `std::string getSQL() const;`
       - **Параметри:** Немає
       - **Повертає:** Згенерований SQL-запит у вигляді рядка

   **Клас `PostgreSQLQueryBuilder`**

   ```cpp
   class PostgreSQLQueryBuilder : public IQueryBuilder {
   private:
       std::string query_;
   public:
       PostgreSQLQueryBuilder();
       IQueryBuilder& select(const std::string& fields) override;
       IQueryBuilder& where(const std::string& condition) override;
       IQueryBuilder& limit(int limit) override;
       std::string getSQL() const override;
   };
   ```

   - **Конструктор:**
     - `PostgreSQLQueryBuilder();`
       - Ініціалізує об'єкт запиту
   - **Методи:**
     - `IQueryBuilder& select(const std::string& fields) override;`
       - **Реалізація:** Додає оператор `SELECT` для PostgreSQL
     - `IQueryBuilder& where(const std::string& condition) override;`
       - **Реалізація:** Додає оператор `WHERE`
     - `IQueryBuilder& limit(int limit) override;`
       - **Реалізація:** Додає оператор `LIMIT`
     - `std::string getSQL() const override;`
       - **Реалізація:** Повертає згенерований SQL-запит

   **Клас `MySQLQueryBuilder`**

   ```cpp
   class MySQLQueryBuilder : public IQueryBuilder {
   private:
       std::string query_;
   public:
       MySQLQueryBuilder();
       IQueryBuilder& select(const std::string& fields) override;
       IQueryBuilder& where(const std::string& condition) override;
       IQueryBuilder& limit(int limit) override;
       std::string getSQL() const override;
   };
   ```

   - **Конструктор:**
     - `MySQLQueryBuilder();`
       - Ініціалізує об'єкт запиту
   - **Методи:**
     - `IQueryBuilder& select(const std::string& fields) override;`
       - **Реалізація:** Додає оператор `SELECT` для MySQL
     - `IQueryBuilder& where(const std::string& condition) override;`
       - **Реалізація:** Додає оператор `WHERE`
     - `IQueryBuilder& limit(int limit) override;`
       - **Реалізація:** Додає оператор `LIMIT`
     - `std::string getSQL() const override;`
       - **Реалізація:** Повертає згенерований SQL-запит

   **Загальна структура класів:**

   ```
   IQueryBuilder (інтерфейс)
   ├── PostgreSQLQueryBuilder (реалізує IQueryBuilder)
   └── MySQLQueryBuilder (реалізує IQueryBuilder)
   ```

3. **Приклад клієнтського коду з використанням звернення до обох СУБД**

   ```cpp
   #include <iostream>
   #include <string>

   // Припустимо, що інтерфейс та класи вже визначені

   void clientCode(IQueryBuilder& builder) {
       std::string sql = builder.select("id, name, email")
                               .where("age > 18")
                               .limit(10)
                               .getSQL();
       std::cout << sql << std::endl;
   }

   int main() {
       PostgreSQLQueryBuilder pgBuilder;
       MySQLQueryBuilder mysqlBuilder;

       std::cout << "PostgreSQL Query:" << std::endl;
       clientCode(pgBuilder);

       std::cout << "\nMySQL Query:" << std::endl;
       clientCode(mysqlBuilder);

       return 0;
   }
   ```

   **Опис:**

   - **Функція `clientCode`:**
     - **Параметри:** `builder` - посилання на об'єкт `IQueryBuilder`
     - **Повертає:** Нічого
     - **Опис:** Використовує будівельник для побудови SQL-запиту
   - **Функція `main`:**
     - Створює об'єкти `PostgreSQLQueryBuilder` та `MySQLQueryBuilder`
     - Викликає `clientCode` для кожного будівельника, демонструючи генерацію SQL-запитів для обох СУБД

   **Вивід програми (приклад):**

   ```
   PostgreSQL Query:
   SELECT id, name, email WHERE age > 18 LIMIT 10;

   MySQL Query:
   SELECT id, name, email WHERE age > 18 LIMIT 10;
   ```

   **Примітка:** В реальній реалізації SQL-запити можуть мати різний синтаксис для різних СУБД. Наприклад, обмеження результатів у PostgreSQL може використовувати `LIMIT`, а в MySQL — `LIMIT`. У даному прикладі синтаксис однаковий, але в реальній ситуації можливі відмінності.

4. **Реалізація методів класів**

   **Клас `PostgreSQLQueryBuilder`**

   ```cpp
   PostgreSQLQueryBuilder::PostgreSQLQueryBuilder() {
       query_ = "";
   }

   IQueryBuilder& PostgreSQLQueryBuilder::select(const std::string& fields) {
       query_ = "SELECT " + fields;
       return *this;
   }

   IQueryBuilder& PostgreSQLQueryBuilder::where(const std::string& condition) {
       query_ += " WHERE " + condition;
       return *this;
   }

   IQueryBuilder& PostgreSQLQueryBuilder::limit(int limit) {
       query_ += " LIMIT " + std::to_string(limit);
       return *this;
   }

   std::string PostgreSQLQueryBuilder::getSQL() const {
       return query_ + ";";
   }
   ```

   **Клас `MySQLQueryBuilder`**

   ```cpp
   MySQLQueryBuilder::MySQLQueryBuilder() {
       query_ = "";
   }

   IQueryBuilder& MySQLQueryBuilder::select(const std::string& fields) {
       query_ = "SELECT " + fields;
       return *this;
   }

   IQueryBuilder& MySQLQueryBuilder::where(const std::string& condition) {
       query_ += " WHERE " + condition;
       return *this;
   }

   IQueryBuilder& MySQLQueryBuilder::limit(int limit) {
       query_ += " LIMIT " + std::to_string(limit);
       return *this;
   }

   std::string MySQLQueryBuilder::getSQL() const {
       return query_ + ";";
   }
   ```

5. **Розширюваність системи**

   Завдяки використанню патерну **Будівельник**, додавання підтримки нових СУБД є простим. Для цього потрібно:

   - Створити новий клас, який успадковує `IQueryBuilder`, та реалізувати його методи згідно з синтаксисом нової СУБД.
   - Використовувати новий будівельник аналогічно до вже існуючих у клієнтському коді.

---

**Висновок:**

У ході виконання лабораторної роботи було розроблено структуру класів та методів для створення власного `QueryBuilder` з використанням патерну проектування **Будівельник**. Система підтримує генерацію SQL-запитів для двох СУБД: PostgreSQL та MySQL. Обидва будівельники мають спільний інтерфейс, що дозволяє використовувати їх взаємозамінно у клієнтському коді. Також наведено приклад клієнтського коду, який демонструє побудову та отримання SQL-запитів для обох СУБД.

Завдяки використанню патерну **Будівельник**, система є легко розширюваною та підтримує додавання нових СУБД у майбутньому.