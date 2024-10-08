**Звіт про виконання лабораторної роботи №5**

**Тема:** Патерн проектування Міст

**Мета:** Здобути навички з реалізацією патерна проектування Міст.

---

**Хід роботи:**

У даній лабораторній роботі необхідно реалізувати систему, яка містить декілька типів сторінок (`Page`) та декілька типів подання (`Renderer`) цих сторінок. Використовуючи патерн проектування **Міст**, ми відокремимо абстракцію (`Page`) від її реалізації (`Renderer`), що дозволить незалежно змінювати як сторінки, так і способи їхнього рендерингу.

**1. Структура класів та методів**

**Інтерфейс `Renderer` (Implementor)**

```cpp
class Renderer {
public:
    virtual std::string renderHeader(const std::string& title) = 0;
    virtual std::string renderContent(const std::string& content) = 0;
    virtual std::string renderFooter() = 0;
    virtual std::string renderParts(const std::map<std::string, std::string>& parts) = 0;
    virtual ~Renderer() {}
};
```

- **Методи:**
  - `std::string renderHeader(const std::string& title);`
    - **Параметри:** `title` — заголовок сторінки.
    - **Повертає:** Рендеринг заголовку у вигляді рядка.
  - `std::string renderContent(const std::string& content);`
    - **Параметри:** `content` — контент сторінки.
    - **Повертає:** Рендеринг контенту у вигляді рядка.
  - `std::string renderFooter();`
    - **Параметри:** Немає.
    - **Повертає:** Рендеринг футера у вигляді рядка.
  - `std::string renderParts(const std::map<std::string, std::string>& parts);`
    - **Параметри:** `parts` — частини інформації про товар.
    - **Повертає:** Рендеринг частин товару у вигляді рядка.

**Конкретні реалізації `Renderer`**

**Клас `HTMLRenderer`**

```cpp
class HTMLRenderer : public Renderer {
public:
    std::string renderHeader(const std::string& title) override;
    std::string renderContent(const std::string& content) override;
    std::string renderFooter() override;
    std::string renderParts(const std::map<std::string, std::string>& parts) override;
};
```

**Клас `JsonRenderer`**

```cpp
class JsonRenderer : public Renderer {
public:
    std::string renderHeader(const std::string& title) override;
    std::string renderContent(const std::string& content) override;
    std::string renderFooter() override;
    std::string renderParts(const std::map<std::string, std::string>& parts) override;
};
```

**Клас `XmlRenderer`**

```cpp
class XmlRenderer : public Renderer {
public:
    std::string renderHeader(const std::string& title) override;
    std::string renderContent(const std::string& content) override;
    std::string renderFooter() override;
    std::string renderParts(const std::map<std::string, std::string>& parts) override;
};
```

**Абстрактний клас `Page` (Abstraction)**

```cpp
class Page {
protected:
    Renderer* renderer;
public:
    Page(Renderer* renderer);
    virtual std::string view() = 0;
    virtual ~Page() {}
};
```

- **Конструктор:**
  - `Page(Renderer* renderer);`
    - **Параметри:** `renderer` — вказівник на об'єкт рендера.
- **Методи:**
  - `virtual std::string view() = 0;`
    - **Параметри:** Немає.
    - **Повертає:** Результат рендерингу сторінки у вигляді рядка.

**Клас `SimplePage`**

```cpp
class SimplePage : public Page {
private:
    std::string title;
    std::string content;
public:
    SimplePage(Renderer* renderer, const std::string& title, const std::string& content);
    std::string view() override;
};
```

- **Конструктор:**
  - `SimplePage(Renderer* renderer, const std::string& title, const std::string& content);`
    - **Параметри:**
      - `renderer` — вказівник на об'єкт рендера.
      - `title` — заголовок сторінки.
      - `content` — контент сторінки.
- **Методи:**
  - `std::string view() override;`
    - **Параметри:** Немає.
    - **Повертає:** Результат рендерингу простої сторінки.

**Клас `Product`**

```cpp
class Product {
public:
    std::string name;
    std::string description;
    std::string image;
    int id;
    Product(const std::string& name, const std::string& description, const std::string& image, int id);
};
```

- **Конструктор:**
  - `Product(const std::string& name, const std::string& description, const std::string& image, int id);`
    - **Параметри:**
      - `name` — назва товару.
      - `description` — опис товару.
      - `image` — зображення товару.
      - `id` — ідентифікатор товару.

**Клас `ProductPage`**

```cpp
class ProductPage : public Page {
private:
    Product* product;
public:
    ProductPage(Renderer* renderer, Product* product);
    std::string view() override;
};
```

- **Конструктор:**
  - `ProductPage(Renderer* renderer, Product* product);`
    - **Параметри:**
      - `renderer` — вказівник на об'єкт рендера.
      - `product` — вказівник на об'єкт товару.
- **Методи:**
  - `std::string view() override;`
    - **Параметри:** Немає.
    - **Повертає:** Результат рендерингу сторінки товару.

**2. Приклад клієнтського коду**

```cpp
#include <iostream>
#include <string>
#include <map>

// Припускаємо, що класи Renderer, HTMLRenderer, JsonRenderer, XmlRenderer, Page, SimplePage, ProductPage, Product вже визначені

int main() {
    // Створюємо об'єкти рендерів
    Renderer* htmlRenderer = new HTMLRenderer();
    Renderer* jsonRenderer = new JsonRenderer();
    Renderer* xmlRenderer = new XmlRenderer();

    // Створюємо просту сторінку з HTML рендером
    Page* simplePageHtml = new SimplePage(htmlRenderer, "Заголовок простої сторінки", "Це контент простої сторінки.");
    std::cout << simplePageHtml->view() << std::endl;

    // Створюємо просту сторінку з JSON рендером
    Page* simplePageJson = new SimplePage(jsonRenderer, "Заголовок простої сторінки", "Це контент простої сторінки.");
    std::cout << simplePageJson->view() << std::endl;

    // Створюємо товар
    Product* product = new Product("Назва товару", "Опис товару", "image.jpg", 123);

    // Створюємо сторінку товару з XML рендером
    Page* productPageXml = new ProductPage(xmlRenderer, product);
    std::cout << productPageXml->view() << std::endl;

    // Створюємо сторінку товару з HTML рендером
    Page* productPageHtml = new ProductPage(htmlRenderer, product);
    std::cout << productPageHtml->view() << std::endl;

    // Очищення пам'яті
    delete simplePageHtml;
    delete simplePageJson;
    delete productPageXml;
    delete productPageHtml;
    delete product;
    delete htmlRenderer;
    delete jsonRenderer;
    delete xmlRenderer;

    return 0;
}
```

**Опис клієнтського коду:**

- **Створюємо різні рендери:** `HTMLRenderer`, `JsonRenderer`, `XmlRenderer`.
- **Створюємо прості сторінки (`SimplePage`)** з різними рендерами та виводимо результат їхнього рендерингу.
- **Створюємо сторінки товару (`ProductPage`)** з різними рендерами та виводимо результат їхнього рендерингу.
- **Використовуємо метод `view()`**, який генерує представлення сторінки з використанням відповідного рендера.
- **Проводимо очищення пам'яті**, видаляючи всі створені об'єкти.

**3. Реалізація методів (без деталей реалізації)**

**Клас `HTMLRenderer`**

```cpp
std::string HTMLRenderer::renderHeader(const std::string& title) {
    // Реалізація рендерингу заголовку у форматі HTML
}

std::string HTMLRenderer::renderContent(const std::string& content) {
    // Реалізація рендерингу контенту у форматі HTML
}

std::string HTMLRenderer::renderFooter() {
    // Реалізація рендерингу футера у форматі HTML
}

std::string HTMLRenderer::renderParts(const std::map<std::string, std::string>& parts) {
    // Реалізація рендерингу частин товару у форматі HTML
}
```

**Клас `JsonRenderer`**

```cpp
std::string JsonRenderer::renderHeader(const std::string& title) {
    // Реалізація рендерингу заголовку у форматі JSON
}

std::string JsonRenderer::renderContent(const std::string& content) {
    // Реалізація рендерингу контенту у форматі JSON
}

std::string JsonRenderer::renderFooter() {
    // Реалізація рендерингу футера у форматі JSON
}

std::string JsonRenderer::renderParts(const std::map<std::string, std::string>& parts) {
    // Реалізація рендерингу частин товару у форматі JSON
}
```

**Клас `XmlRenderer`**

```cpp
std::string XmlRenderer::renderHeader(const std::string& title) {
    // Реалізація рендерингу заголовку у форматі XML
}

std::string XmlRenderer::renderContent(const std::string& content) {
    // Реалізація рендерингу контенту у форматі XML
}

std::string XmlRenderer::renderFooter() {
    // Реалізація рендерингу футера у форматі XML
}

std::string XmlRenderer::renderParts(const std::map<std::string, std::string>& parts) {
    // Реалізація рендерингу частин товару у форматі XML
}
```

**Клас `SimplePage`**

```cpp
SimplePage::SimplePage(Renderer* renderer, const std::string& title, const std::string& content)
    : Page(renderer), title(title), content(content) {}

std::string SimplePage::view() {
    std::string result;
    result += renderer->renderHeader(title);
    result += renderer->renderContent(content);
    result += renderer->renderFooter();
    return result;
}
```

**Клас `ProductPage`**

```cpp
ProductPage::ProductPage(Renderer* renderer, Product* product)
    : Page(renderer), product(product) {}

std::string ProductPage::view() {
    std::map<std::string, std::string> parts;
    parts["name"] = product->name;
    parts["description"] = product->description;
    parts["image"] = product->image;
    parts["id"] = std::to_string(product->id);

    std::string result;
    result += renderer->renderHeader("Сторінка товару");
    result += renderer->renderParts(parts);
    result += renderer->renderFooter();
    return result;
}
```

---  

**4. Висновок**

У ході виконання лабораторної роботи було створено структуру класів та методів, яка демонструє реалізацію патерна проектування **Міст** та вирішує поставлене завдання. Розроблена система дозволяє незалежно розширювати як типи сторінок (`Page`), так і способи їхнього рендерингу (`Renderer`), що забезпечує гнучкість та масштабованість системи. Приклад клієнтського коду демонструє можливість рендерингу різних типів сторінок з використанням різних форматів подання (HTML, JSON, XML).

