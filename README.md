**Звіт про виконання лабораторної роботи №9**

**Тема:** Патерн проектування "Відвідувач"

**Мета:** Здобути навички реалізації патерна "Відвідувач".

---

### Хід роботи

### 1. Теоретична частина

**Патерн "Відвідувач"** (Visitor) – це поведінковий патерн, який дозволяє додавати нові операції до вже існуючих класів без зміни їхньої структури. Він корисний, коли треба виконувати різні операції над об’єктами різних типів, об’єднаних загальним інтерфейсом або базовим класом. У нашому випадку цей патерн дозволить згенерувати "Зарплатну відомість" як для всієї компанії, так і для окремих департаментів.

### 2. Опис завдання

У нашій системі є три сутності:
1. **Компанія** – містить масив департаментів.
2. **Департамент** – містить масив співробітників.
3. **Співробітник** – має назву позиції та розмір зарплати.

Патерн "Відвідувач" дозволить додати можливість генерації звіту про зарплату без зміни існуючої структури компанії, департаментів та співробітників.

### 3. Проектування класів

Для реалізації патерна "Відвідувач" необхідно:
- Створити інтерфейс `Visitor`, який матиме методи для обробки кожної з трьох сутностей: `Company`, `Department`, `Employee`.
- Реалізувати конкретного відвідувача `SalaryReportVisitor`, який генеруватиме звіт про зарплати.
- Визначити базові методи для прийняття відвідувача в кожному з класів-сутностей (`Company`, `Department`, `Employee`).

### 4. Реалізація класів

Нижче представлена структура класів та методів без реалізації самих методів.

#### 4.1. Інтерфейс "Відвідувача"

```cpp
// Інтерфейс відвідувача
class Visitor {
public:
    virtual void visitCompany(const Company& company) = 0;
    virtual void visitDepartment(const Department& department) = 0;
    virtual void visitEmployee(const Employee& employee) = 0;
    virtual ~Visitor() = default;
};
```

#### 4.2. Клас `SalaryReportVisitor`

Клас, який реалізує "Відвідувача" та генерує звіт про зарплати для компанії або департаменту.

```cpp
// Конкретний відвідувач для створення звіту про зарплати
class SalaryReportVisitor : public Visitor {
public:
    void visitCompany(const Company& company) override;
    void visitDepartment(const Department& department) override;
    void visitEmployee(const Employee& employee) override;

    // Метод для отримання згенерованого звіту
    std::string getReport() const;
};
```

#### 4.3. Класи-сутності: `Company`, `Department`, `Employee`

Кожен клас-сутність реалізує метод `accept`, який приймає відвідувача і дозволяє йому виконувати певну операцію.

```cpp
#include <vector>
#include <string>

class Department; // Попереднє оголошення для уникнення циклічної залежності

// Клас Співробітник
class Employee {
private:
    std::string position;
    double salary;

public:
    Employee(const std::string& position, double salary) : position(position), salary(salary) {}

    // Метод для отримання позиції співробітника
    std::string getPosition() const { return position; }

    // Метод для отримання зарплати співробітника
    double getSalary() const { return salary; }

    // Метод прийняття відвідувача
    void accept(Visitor& visitor) const {
        visitor.visitEmployee(*this);
    }
};

// Клас Департамент
class Department {
private:
    std::vector<Employee> employees;

public:
    Department(const std::vector<Employee>& employees) : employees(employees) {}

    // Метод для отримання співробітників департаменту
    const std::vector<Employee>& getEmployees() const { return employees; }

    // Метод прийняття відвідувача
    void accept(Visitor& visitor) const {
        visitor.visitDepartment(*this);
        for (const auto& employee : employees) {
            employee.accept(visitor);
        }
    }
};

// Клас Компанія
class Company {
private:
    std::vector<Department> departments;

public:
    Company(const std::vector<Department>& departments) : departments(departments) {}

    // Метод для отримання департаментів компанії
    const std::vector<Department>& getDepartments() const { return departments; }

    // Метод прийняття відвідувача
    void accept(Visitor& visitor) const {
        visitor.visitCompany(*this);
        for (const auto& department : departments) {
            department.accept(visitor);
        }
    }
};
```

### 5. Опис роботи класів

1. **Visitor** – це інтерфейс для класів-відвідувачів. Він містить методи для обробки кожної з сутностей: `visitCompany`, `visitDepartment` і `visitEmployee`.

2. **SalaryReportVisitor** – конкретний клас відвідувача, який генерує звіт про зарплати. Він реалізує методи `visitCompany`, `visitDepartment` та `visitEmployee`. Метод `getReport()` дозволяє отримати згенерований звіт у вигляді рядка.

3. **Employee** – клас співробітника, який має позицію і зарплату. Він реалізує метод `accept` для прийняття відвідувача і передає себе для обробки у відповідний метод відвідувача.

4. **Department** – клас департаменту, що містить масив співробітників. Його метод `accept` приймає відвідувача і, окрім виклику `visitDepartment`, також обробляє кожного співробітника в департаменті.

5. **Company** – клас компанії, який містить масив департаментів. Він приймає відвідувача через метод `accept`, що дозволяє обробити компанію в цілому та її департаменти й співробітників.

### 6. Використання (клієнтський код)

Приклад коду для отримання зарплатної відомості для компанії та конкретного департаменту:

```cpp
int main() {
    // Створення співробітників
    Employee emp1("Manager", 5000);
    Employee emp2("Developer", 4000);
    Employee emp3("Designer", 3000);

    // Створення департаменту з масивом співробітників
    Department dept1({ emp1, emp2 });
    Department dept2({ emp3 });

    // Створення компанії з масивом департаментів
    Company company({ dept1, dept2 });

    // Створення відвідувача для зарплатного звіту
    SalaryReportVisitor salaryVisitor;

    // Генерація звіту для всієї компанії
    company.accept(salaryVisitor);
    std::cout << "Зарплатна відомість для компанії:\n" << salaryVisitor.getReport() << std::endl;

    // Генерація звіту для конкретного департаменту
    SalaryReportVisitor deptSalaryVisitor;
    dept1.accept(deptSalaryVisitor);
    std::cout << "Зарплатна відомість для департаменту:\n" << deptSalaryVisitor.getReport() << std::endl;

    return 0;
}
```
---
### Висновок

У цій лабораторній роботі було реалізовано патерн "Відвідувач" для створення звіту "Зарплатна відомість". Це дозволило генерувати звіт як для компанії в цілому, так і для окремих департаментів. Завдяки патерну "Відвідувач" була досягнута гнучкість у додаванні нових операцій над сутностями без зміни їхніх класів, що є перевагою при роботі з ієрархією об’єктів, які мають спільний інтерфейс.