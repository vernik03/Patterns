**Звіт про виконання лабораторної роботи №4**

**Тема:** Патерн проектування Адаптер

**Мета:** Здобути навички з реалізацією патерна проектування Адаптер.

---

**Хід роботи:**

У даній лабораторній роботі необхідно реалізувати систему сповіщень з можливістю відправки повідомлень по email, у месенджер Slack та через SMS, використовуючи патерн проектування Адаптер. При цьому інтерфейс `Notification` змінювати не можна.

**1. Інтерфейс `Notification`**

```cpp
class Notification {
public:
    virtual void send(const std::string& title, const std::string& message) = 0;
    virtual ~Notification() {}
};
```

- **Метод:**
  - `void send(const std::string& title, const std::string& message);`
    - **Параметри:**
      - `title` — заголовок повідомлення.
      - `message` — текст повідомлення.
    - **Повертає:** Нічого.

**2. Клас `EmailNotification`**

```cpp
class EmailNotification : public Notification {
private:
    std::string adminEmail;
public:
    EmailNotification(const std::string& adminEmail);
    void send(const std::string& title, const std::string& message) override;
};
```

- **Конструктор:**
  - `EmailNotification(const std::string& adminEmail);`
    - **Параметри:**
      - `adminEmail` — email адміністратора, на який відправляються сповіщення.
- **Метод:**
  - `void send(const std::string& title, const std::string& message) override;`
    - **Параметри:**
      - `title` — заголовок повідомлення.
      - `message` — текст повідомлення.
    - **Повертає:** Нічого.

**3. Адаптери для Slack та SMS**

**Клас `SlackApi` (Adaptee)**

```cpp
class SlackApi {
private:
    std::string login;
    std::string apiKey;
public:
    SlackApi(const std::string& login, const std::string& apiKey);
    void sendMessage(const std::string& chatId, const std::string& message);
};
```

- **Конструктор:**
  - `SlackApi(const std::string& login, const std::string& apiKey);`
    - **Параметри:**
      - `login` — логін користувача в Slack.
      - `apiKey` — API ключ для доступу до Slack.
- **Метод:**
  - `void sendMessage(const std::string& chatId, const std::string& message);`
    - **Параметри:**
      - `chatId` — ідентифікатор чату.
      - `message` — текст повідомлення.
    - **Повертає:** Нічого.

**Клас `SlackNotification` (Adapter)**

```cpp
class SlackNotification : public Notification {
private:
    SlackApi slackApi;
    std::string chatId;
public:
    SlackNotification(const std::string& login, const std::string& apiKey, const std::string& chatId);
    void send(const std::string& title, const std::string& message) override;
};
```

- **Конструктор:**
  - `SlackNotification(const std::string& login, const std::string& apiKey, const std::string& chatId);`
    - **Параметри:**
      - `login` — логін користувача в Slack.
      - `apiKey` — API ключ для доступу до Slack.
      - `chatId` — ідентифікатор чату для відправки повідомлень.
- **Метод:**
  - `void send(const std::string& title, const std::string& message) override;`
    - **Параметри:**
      - `title` — заголовок повідомлення.
      - `message` — текст повідомлення.
    - **Повертає:** Нічого.
    - **Опис:** Формує повідомлення та використовує `SlackApi` для відправки.

**Клас `SmsSender` (Adaptee)**

```cpp
class SmsSender {
private:
    std::string sender;
public:
    SmsSender(const std::string& sender);
    void sendSms(const std::string& phone, const std::string& message);
};
```

- **Конструктор:**
  - `SmsSender(const std::string& sender);`
    - **Параметри:**
      - `sender` — ім'я відправника SMS.
- **Метод:**
  - `void sendSms(const std::string& phone, const std::string& message);`
    - **Параметри:**
      - `phone` — номер телефону отримувача.
      - `message` — текст повідомлення.
    - **Повертає:** Нічого.

**Клас `SMSNotification` (Adapter)**

```cpp
class SMSNotification : public Notification {
private:
    SmsSender smsSender;
    std::string phone;
public:
    SMSNotification(const std::string& phone, const std::string& sender);
    void send(const std::string& title, const std::string& message) override;
};
```

- **Конструктор:**
  - `SMSNotification(const std::string& phone, const std::string& sender);`
    - **Параметри:**
      - `phone` — номер телефону отримувача.
      - `sender` — ім'я відправника SMS.
- **Метод:**
  - `void send(const std::string& title, const std::string& message) override;`
    - **Параметри:**
      - `title` — заголовок повідомлення.
      - `message` — текст повідомлення.
    - **Повертає:** Нічого.
    - **Опис:** Формує повідомлення та використовує `SmsSender` для відправки.

**4. Приклад клієнтського коду**

```cpp
#include <iostream>

int main() {
    // Відправка email
    Notification* emailNotification = new EmailNotification("admin@example.com");
    emailNotification->send("Тестове повідомлення", "Це тестове email-сповіщення.");

    // Відправка повідомлення у Slack
    Notification* slackNotification = new SlackNotification("user_login", "api_key_123", "channel_id");
    slackNotification->send("Тестове повідомлення", "Це тестове повідомлення у Slack.");

    // Відправка SMS
    Notification* smsNotification = new SMSNotification("+380123456789", "MyApp");
    smsNotification->send("Тестове повідомлення", "Це тестове SMS-сповіщення.");

    delete emailNotification;
    delete slackNotification;
    delete smsNotification;

    return 0;
}
```

**Опис:**

- **EmailNotification:**
  - Створюється об'єкт `EmailNotification` з адресою адміністратора.
  - Викликається метод `send` для відправки повідомлення.
- **SlackNotification:**
  - Створюється об'єкт `SlackNotification` з необхідними даними для авторизації та ідентифікатором чату.
  - Викликається метод `send` для відправки повідомлення.
- **SMSNotification:**
  - Створюється об'єкт `SMSNotification` з номером телефону та ім'ям відправника.
  - Викликається метод `send` для відправки повідомлення.

**5. Висновок**

У ході виконання лабораторної роботи було розроблено структуру класів та методів для системи сповіщень з використанням патерну проектування **Адаптер**. Створені адаптери для Slack та SMS дозволяють інтегрувати нові способи відправки сповіщень без зміни існуючого інтерфейсу `Notification`. Це демонструє гнучкість та масштабованість системи при додаванні нових функціональних можливостей.

---