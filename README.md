**Звіт про виконання лабораторної роботи №10**

**Тема:** Патерн проектування Посередник.

**Мета:** Здобути навички з реалізацією патерна проектування Посередник.

**Хід роботи:**

Для реалізації завдання необхідно створити структуру класів та методів, яка буде демонструвати реалізацію патерна Посередник і вирішувати описане завдання.

**Реалізація:**

1. **Інтерфейс Посередника**

```cpp
// IOrderFormMediator.h
class Component;

class IOrderFormMediator {
public:
    virtual void Notify(Component* sender, const std::string& event) = 0;
    virtual ~IOrderFormMediator() {}
};
```

2. **Базовий клас Компонента**

```cpp
// Component.h
class Component {
protected:
    IOrderFormMediator* mediator;
public:
    Component(IOrderFormMediator* mediator = nullptr) : mediator(mediator) {}
    void SetMediator(IOrderFormMediator* mediator) {
        this->mediator = mediator;
    }
    virtual void SetEnabled(bool enabled) = 0;
};
```

3. **Конкретні Компоненти**

- **DatePicker** (компонент вибору дати доставки)

```cpp
// DatePicker.h
class DatePicker : public Component {
private:
    std::string selectedDate;
    bool enabled;
public:
    DatePicker(IOrderFormMediator* mediator = nullptr);
    void SetDate(const std::string& date);
    std::string GetDate() const;
    void SetEnabled(bool enabled) override;
    bool IsEnabled() const;
};
```

- **TimeSlotSelector** (компонент вибору проміжку часу)

```cpp
// TimeSlotSelector.h
class TimeSlotSelector : public Component {
private:
    std::vector<std::string> availableTimeSlots;
    std::string selectedTimeSlot;
    bool enabled;
public:
    TimeSlotSelector(IOrderFormMediator* mediator = nullptr);
    void SetAvailableTimeSlots(const std::vector<std::string>& timeSlots);
    void SetTimeSlot(const std::string& timeSlot);
    std::string GetTimeSlot() const;
    void SetEnabled(bool enabled) override;
    bool IsEnabled() const;
};
```

- **RecipientInfo** (інформація про отримувача)

```cpp
// RecipientInfo.h
class RecipientInfo : public Component {
private:
    std::string name;
    std::string phone;
    bool isVisible;
    bool enabled;
public:
    RecipientInfo(IOrderFormMediator* mediator = nullptr);
    void SetName(const std::string& name);
    void SetPhone(const std::string& phone);
    std::string GetName() const;
    std::string GetPhone() const;
    void SetVisibility(bool visible);
    bool IsVisible() const;
    void SetEnabled(bool enabled) override;
    bool IsEnabled() const;
};
```

- **OtherRecipientCheckbox** (чекбокс "отримувач інша особа")

```cpp
// OtherRecipientCheckbox.h
class OtherRecipientCheckbox : public Component {
private:
    bool isChecked;
    bool enabled;
public:
    OtherRecipientCheckbox(IOrderFormMediator* mediator = nullptr);
    void SetChecked(bool checked);
    bool IsChecked() const;
    void SetEnabled(bool enabled) override;
    bool IsEnabled() const;
};
```

- **SelfPickupCheckbox** (чекбокс "самостійно забрати з магазину")

```cpp
// SelfPickupCheckbox.h
class SelfPickupCheckbox : public Component {
private:
    bool isChecked;
    bool enabled;
public:
    SelfPickupCheckbox(IOrderFormMediator* mediator = nullptr);
    void SetChecked(bool checked);
    bool IsChecked() const;
    void SetEnabled(bool enabled) override;
    bool IsEnabled() const;
};
```

4. **Конкретний Посередник**

```cpp
// OrderFormMediator.h
class OrderFormMediator : public IOrderFormMediator {
private:
    DatePicker* datePicker;
    TimeSlotSelector* timeSlotSelector;
    RecipientInfo* recipientInfo;
    OtherRecipientCheckbox* otherRecipientCheckbox;
    SelfPickupCheckbox* selfPickupCheckbox;
public:
    OrderFormMediator(DatePicker* dp, TimeSlotSelector* ts, RecipientInfo* ri, OtherRecipientCheckbox* orc, SelfPickupCheckbox* spc);
    void Notify(Component* sender, const std::string& event) override;
};
```

5. **Реалізація методів компонентів**

- **DatePicker.cpp**

```cpp
DatePicker::DatePicker(IOrderFormMediator* mediator) : Component(mediator), enabled(true) {}

void DatePicker::SetDate(const std::string& date) {
    selectedDate = date;
    if (mediator) {
        mediator->Notify(this, "DateChanged");
    }
}

std::string DatePicker::GetDate() const {
    return selectedDate;
}

void DatePicker::SetEnabled(bool enabled) {
    this->enabled = enabled;
}

bool DatePicker::IsEnabled() const {
    return enabled;
}
```

- **TimeSlotSelector.cpp**

```cpp
TimeSlotSelector::TimeSlotSelector(IOrderFormMediator* mediator) : Component(mediator), enabled(true) {}

void TimeSlotSelector::SetAvailableTimeSlots(const std::vector<std::string>& timeSlots) {
    availableTimeSlots = timeSlots;
}

void TimeSlotSelector::SetTimeSlot(const std::string& timeSlot) {
    selectedTimeSlot = timeSlot;
}

std::string TimeSlotSelector::GetTimeSlot() const {
    return selectedTimeSlot;
}

void TimeSlotSelector::SetEnabled(bool enabled) {
    this->enabled = enabled;
}

bool TimeSlotSelector::IsEnabled() const {
    return enabled;
}
```

- **RecipientInfo.cpp**

```cpp
RecipientInfo::RecipientInfo(IOrderFormMediator* mediator) : Component(mediator), isVisible(false), enabled(true) {}

void RecipientInfo::SetName(const std::string& name) {
    this->name = name;
}

void RecipientInfo::SetPhone(const std::string& phone) {
    this->phone = phone;
}

std::string RecipientInfo::GetName() const {
    return name;
}

std::string RecipientInfo::GetPhone() const {
    return phone;
}

void RecipientInfo::SetVisibility(bool visible) {
    isVisible = visible;
}

bool RecipientInfo::IsVisible() const {
    return isVisible;
}

void RecipientInfo::SetEnabled(bool enabled) {
    this->enabled = enabled;
}

bool RecipientInfo::IsEnabled() const {
    return enabled;
}
```

- **OtherRecipientCheckbox.cpp**

```cpp
OtherRecipientCheckbox::OtherRecipientCheckbox(IOrderFormMediator* mediator) : Component(mediator), isChecked(false), enabled(true) {}

void OtherRecipientCheckbox::SetChecked(bool checked) {
    isChecked = checked;
    if (mediator) {
        mediator->Notify(this, "OtherRecipientChanged");
    }
}

bool OtherRecipientCheckbox::IsChecked() const {
    return isChecked;
}

void OtherRecipientCheckbox::SetEnabled(bool enabled) {
    this->enabled = enabled;
}

bool OtherRecipientCheckbox::IsEnabled() const {
    return enabled;
}
```

- **SelfPickupCheckbox.cpp**

```cpp
SelfPickupCheckbox::SelfPickupCheckbox(IOrderFormMediator* mediator) : Component(mediator), isChecked(false), enabled(true) {}

void SelfPickupCheckbox::SetChecked(bool checked) {
    isChecked = checked;
    if (mediator) {
        mediator->Notify(this, "SelfPickupChanged");
    }
}

bool SelfPickupCheckbox::IsChecked() const {
    return isChecked;
}

void SelfPickupCheckbox::SetEnabled(bool enabled) {
    this->enabled = enabled;
}

bool SelfPickupCheckbox::IsEnabled() const {
    return enabled;
}
```

6. **Реалізація методів Посередника**

- **OrderFormMediator.cpp**

```cpp
OrderFormMediator::OrderFormMediator(DatePicker* dp, TimeSlotSelector* ts, RecipientInfo* ri, OtherRecipientCheckbox* orc, SelfPickupCheckbox* spc)
    : datePicker(dp), timeSlotSelector(ts), recipientInfo(ri), otherRecipientCheckbox(orc), selfPickupCheckbox(spc) {
    datePicker->SetMediator(this);
    timeSlotSelector->SetMediator(this);
    recipientInfo->SetMediator(this);
    otherRecipientCheckbox->SetMediator(this);
    selfPickupCheckbox->SetMediator(this);
}

void OrderFormMediator::Notify(Component* sender, const std::string& event) {
    if (sender == datePicker && event == "DateChanged") {
        // Оновити список доступних проміжків часу
        std::vector<std::string> timeSlots = /* логіка отримання доступних проміжків часу на основі datePicker->GetDate() */;
        timeSlotSelector->SetAvailableTimeSlots(timeSlots);
    } else if (sender == otherRecipientCheckbox && event == "OtherRecipientChanged") {
        bool isChecked = otherRecipientCheckbox->IsChecked();
        recipientInfo->SetVisibility(isChecked);
        // Поля ім'я та телефон стають обов'язковими, якщо isChecked == true
    } else if (sender == selfPickupCheckbox && event == "SelfPickupChanged") {
        bool isChecked = selfPickupCheckbox->IsChecked();
        // Вимкнути або увімкнути елементи форми доставки
        datePicker->SetEnabled(!isChecked);
        timeSlotSelector->SetEnabled(!isChecked);
        otherRecipientCheckbox->SetEnabled(!isChecked);
        recipientInfo->SetEnabled(!isChecked && otherRecipientCheckbox->IsChecked());
    }
}
```

**Висновок:**

У ході виконання лабораторної роботи було створено структуру класів та методів, яка демонструє реалізацію патерна Посередник для сторінки оформлення замовлення в службі доставки квітів. Розроблена система дозволяє компонентам форми взаємодіяти через посередника, що забезпечує слабке зчеплення та гнучкість у взаємодії компонентів.