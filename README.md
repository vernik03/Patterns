### Звіт про виконання лабораторної роботи №7

**Тема:** Патерн проектування Стратегія

**Мета:** Здобуття навичок із реалізації патерну проектування "Стратегія".

---

### Хід роботи

### 1. Теоретична частина

**Патерн "Стратегія"** є поведінковим патерном, який дає змогу визначити сімейство алгоритмів, інкапсулювати кожен із них та зробити їх взаємозамінними. Патерн дозволяє змінювати алгоритми незалежно від клієнтів, які їх використовують. 

У нашій задачі це дозволить нам створити різні стратегії для розрахунку вартості доставки, які можна обирати та змінювати в залежності від умов або побажань користувача.

### 2. Опис завдання

Ми розробляємо додаток для доставки їжі, який має три способи доставки:
1. Самовивіз
2. Доставка зовнішньою службою доставки
3. Доставка власною службою доставки

Після того, як користувач обере бажаний спосіб доставки, додаток має розрахувати вартість відповідно до вибраної стратегії.

### 3. Проектування класів

Для реалізації патерну "Стратегія" в нашому додатку необхідно створити:
- Абстрактний клас `DeliveryStrategy`, який буде інтерфейсом для всіх стратегій доставки.
- Конкретні стратегії для кожного способу доставки.
- Клас `DeliveryCostCalculator`, який використовуватиме обрану стратегію для розрахунку вартості.

### 4. Реалізація класів

Нижче наведено структуру класів та методів без реалізації самих методів.

#### 4.1. Інтерфейс стратегії доставки

```cpp
// Інтерфейс для всіх стратегій доставки
class DeliveryStrategy {
public:
    virtual double calculateCost(double distance, double weight) const = 0;
    virtual ~DeliveryStrategy() = default;
};
```

#### 4.2. Конкретні стратегії

Кожен з класів нижче представляє конкретний спосіб доставки.

```cpp
// Стратегія для самовивозу
class PickupStrategy : public DeliveryStrategy {
public:
    double calculateCost(double distance, double weight) const override;
};

// Стратегія для доставки зовнішньою службою
class ExternalDeliveryStrategy : public DeliveryStrategy {
public:
    double calculateCost(double distance, double weight) const override;
};

// Стратегія для доставки власною службою
class OwnDeliveryStrategy : public DeliveryStrategy {
public:
    double calculateCost(double distance, double weight) const override;
};
```

#### 4.3. Контекстний клас для розрахунку вартості доставки

Клас `DeliveryCostCalculator` виступає як контекст для обраної стратегії. Він надає інтерфейс для вибору стратегії доставки та розрахунку вартості.

```cpp
// Контекстний клас для розрахунку вартості доставки
class DeliveryCostCalculator {
private:
    DeliveryStrategy* strategy;
public:
    // Конструктор з передачею стратегії
    explicit DeliveryCostCalculator(DeliveryStrategy* strategy) : strategy(strategy) {}

    // Метод для зміни стратегії доставки
    void setStrategy(DeliveryStrategy* newStrategy) {
        strategy = newStrategy;
    }

    // Метод для розрахунку вартості доставки з використанням обраної стратегії
    double calculateDeliveryCost(double distance, double weight) const {
        return strategy->calculateCost(distance, weight);
    }
};
```

### 5. Опис роботи класів

1. **DeliveryStrategy** – це абстрактний клас, який визначає інтерфейс для всіх стратегій доставки. Він має віртуальний метод `calculateCost()`, який приймає відстань та вагу і повертає вартість доставки.
  
2. **PickupStrategy, ExternalDeliveryStrategy, OwnDeliveryStrategy** – кожен із цих класів реалізує метод `calculateCost()` відповідно до своїх правил розрахунку. Наприклад, `PickupStrategy` може повертати нульову вартість, оскільки самовивіз не потребує доставки.

3. **DeliveryCostCalculator** – контекстний клас, який зберігає поточну стратегію доставки і надає можливість обирати іншу стратегію через метод `setStrategy()`. Метод `calculateDeliveryCost()` використовує встановлену стратегію для обчислення вартості доставки.

### 6. Використання

Приклад використання класів у головній функції:

```cpp
int main() {
    // Приклад створення стратегії
    PickupStrategy pickup;
    ExternalDeliveryStrategy external;
    OwnDeliveryStrategy own;

    // Створення об'єкта калькулятора з початковою стратегією самовивозу
    DeliveryCostCalculator calculator(&pickup);

    // Розрахунок вартості доставки для самовивозу
    double cost = calculator.calculateDeliveryCost(10, 2);
    
    // Зміна стратегії на доставку зовнішньою службою
    calculator.setStrategy(&external);
    cost = calculator.calculateDeliveryCost(10, 2);

    // Зміна стратегії на власну службу доставки
    calculator.setStrategy(&own);
    cost = calculator.calculateDeliveryCost(10, 2);

    return 0;
}
```
---

### Висновок

У ході лабораторної роботи було розроблено структуру класів для додатку доставки їжі із застосуванням патерну "Стратегія". Цей патерн дозволив створити модульну систему розрахунку вартості доставки з можливістю легкого додавання або зміни способів доставки без потреби вносити зміни в основний код.