**Звіт про виконання лабораторної роботи №2**

**Тема:** Патерн проектування Фабричний метод (Factory Method)

**Мета:** Здобути навички з реалізацією патерну проектування Фабричний метод.

---

**Хід роботи:**

1. **Реалізація системи публікації повідомлень у соціальних мережах**

   Для реалізації системи, що публікує повідомлення у різних соціальних мережах, використаємо патерн Фабричний метод. Це дозволить нам створювати об'єкти соціальних мереж динамічно, залежно від потреби, та легко розширювати систему новими соціальними мережами в майбутньому.

2. **Структура класів та методів**

   **Абстрактний клас `SocialNetwork`**

   ```cpp
   class SocialNetwork {
   public:
       virtual void login() = 0;
       virtual void publishPost(const std::string& content) = 0;
       virtual ~SocialNetwork() {}
   };
   ```

   - **Методи:**
     - `void login();`
       - **Параметри:** Немає
       - **Повертає:** Нічого
     - `void publishPost(const std::string& content);`
       - **Параметри:** `content` - текст повідомлення для публікації
       - **Повертає:** Нічого

   **Клас `Facebook`**

   ```cpp
   class Facebook : public SocialNetwork {
   private:
       std::string login_;
       std::string password_;
   public:
       Facebook(const std::string& login, const std::string& password);
       void login() override;
       void publishPost(const std::string& content) override;
   };
   ```

   - **Конструктор:**
     - `Facebook(const std::string& login, const std::string& password);`
       - **Параметри:** `login` - логін користувача, `password` - пароль
   - **Методи:**
     - `void login() override;`
       - **Параметри:** Немає
       - **Повертає:** Нічого
     - `void publishPost(const std::string& content) override;`
       - **Параметри:** `content` - текст повідомлення
       - **Повертає:** Нічого

   **Клас `LinkedIn`**

   ```cpp
   class LinkedIn : public SocialNetwork {
   private:
       std::string email_;
       std::string password_;
   public:
       LinkedIn(const std::string& email, const std::string& password);
       void login() override;
       void publishPost(const std::string& content) override;
   };
   ```

   - **Конструктор:**
     - `LinkedIn(const std::string& email, const std::string& password);`
       - **Параметри:** `email` - електронна пошта користувача, `password` - пароль
   - **Методи:**
     - `void login() override;`
       - **Параметри:** Немає
       - **Повертає:** Нічого
     - `void publishPost(const std::string& content) override;`
       - **Параметри:** `content` - текст повідомлення
       - **Повертає:** Нічого

   **Абстрактний клас `SocialNetworkPoster` (Фабрика)**

   ```cpp
   class SocialNetworkPoster {
   public:
       virtual SocialNetwork* createSocialNetwork() = 0;
       void post(const std::string& content);
       virtual ~SocialNetworkPoster() {}
   };
   ```

   - **Методи:**
     - `SocialNetwork* createSocialNetwork();`
       - **Параметри:** Немає
       - **Повертає:** Вказівник на об'єкт `SocialNetwork`
     - `void post(const std::string& content);`
       - **Параметри:** `content` - текст повідомлення
       - **Повертає:** Нічого

   **Клас `FacebookPoster`**

   ```cpp
   class FacebookPoster : public SocialNetworkPoster {
   private:
       std::string login_;
       std::string password_;
   public:
       FacebookPoster(const std::string& login, const std::string& password);
       SocialNetwork* createSocialNetwork() override;
   };
   ```

   - **Конструктор:**
     - `FacebookPoster(const std::string& login, const std::string& password);`
       - **Параметри:** `login` - логін користувача, `password` - пароль
   - **Методи:**
     - `SocialNetwork* createSocialNetwork() override;`
       - **Параметри:** Немає
       - **Повертає:** Вказівник на об'єкт `Facebook`

   **Клас `LinkedInPoster`**

   ```cpp
   class LinkedInPoster : public SocialNetworkPoster {
   private:
       std::string email_;
       std::string password_;
   public:
       LinkedInPoster(const std::string& email, const std::string& password);
       SocialNetwork* createSocialNetwork() override;
   };
   ```

   - **Конструктор:**
     - `LinkedInPoster(const std::string& email, const std::string& password);`
       - **Параметри:** `email` - електронна пошта користувача, `password` - пароль
   - **Методи:**
     - `SocialNetwork* createSocialNetwork() override;`
       - **Параметри:** Немає
       - **Повертає:** Вказівник на об'єкт `LinkedIn`

3. **Приклад публікації повідомлень в обох соціальних мережах**

   ```cpp
   void clientCode(SocialNetworkPoster* poster) {
       poster->post("Привіт, це тестове повідомлення!");
   }

   int main() {
       SocialNetworkPoster* facebookPoster = new FacebookPoster("user_login", "user_password");
       SocialNetworkPoster* linkedInPoster = new LinkedInPoster("user_email@example.com", "user_password");

       clientCode(facebookPoster);
       clientCode(linkedInPoster);

       delete facebookPoster;
       delete linkedInPoster;

       return 0;
   }
   ```

   - **Функція `clientCode`:**
     - **Параметри:** `poster` - вказівник на об'єкт `SocialNetworkPoster`
     - **Повертає:** Нічого
     - **Опис:** Використовує фабрику для створення соціальної мережі та публікує повідомлення.

   - **Функція `main`:**
     - Створює об'єкти `FacebookPoster` та `LinkedInPoster` з відповідними даними для входу.
     - Викликає `clientCode` для публікації повідомлення в кожній соціальній мережі.

4. **Можливість розширення системи**

   Завдяки використанню патерну Фабричний метод, додавання нових соціальних мереж є простим. Для цього потрібно:

   - Створити новий клас, що успадковує `SocialNetwork`, і реалізувати його методи.
   - Створити відповідний клас-фабрику, що успадковує `SocialNetworkPoster`, і реалізувати метод `createSocialNetwork()`.

---

**Висновок:**

У ході виконання лабораторної роботи було розроблено структуру класів та методів для системи публікації повідомлень у соціальних мережах з використанням патерну проектування Фабричний метод. Створена система дозволяє легко додавати нові соціальні мережі та забезпечує гнучкість у розширенні функціоналу в майбутньому. Приклад використання системи демонструє публікацію повідомлень у Facebook та LinkedIn.