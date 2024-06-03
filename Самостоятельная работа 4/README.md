# Задание 4.1: Проектирование с учетом 3НФ

## Проектирование базы данных в 3НФ

### Шаг 1: Определение сущностей и их атрибутов

На основе предоставленных данных можно выделить три основные сущности:
1. **Товары (Products)**
2. **Заказчики (Customers)**
3. **Заказы (Orders)**
4. **Позиции заказа (OrderItems)**

---

### Шаг 2: Определение таблиц и их атрибутов

#### Таблица Products
- **CatalogNum** (PRIMARY KEY, NOT NULL): Каталожный номер товара
- **Product** (NOT NULL): Название товара
- **Price** (NOT NULL): Цена за единицу товара

#### Таблица Customers
- **CustomerNum** (PRIMARY KEY, NOT NULL): Номер заказчика
- **CustomerName** (NOT NULL): Фамилия и инициалы заказчика
- **CustomerAddress** (NOT NULL): Адрес заказчика
- **CustomerPhone** (NOT NULL): Телефон заказчика

#### Таблица Orders
- **OrderNum** (PRIMARY KEY, NOT NULL): Номер заказа
- **CustomerNum** (FOREIGN KEY, NOT NULL): Номер заказчика (ссылка на таблицу Customers)

#### Таблица OrderItems
- **OrderNum** (FOREIGN KEY, NOT NULL): Номер заказа (ссылка на таблицу Orders)
- **CatalogNum** (FOREIGN KEY, NOT NULL): Каталожный номер товара (ссылка на таблицу Products)
- **Quantity** (NOT NULL): Количество единиц указанного товара в заказе

Первичный ключ в таблице **OrderItems** будет составным и состоять из полей **OrderNum** и **CatalogNum**.

---

### Шаг 3: Определение связей между таблицами

- **Orders** → **Customers**: Каждый заказ связан с одним заказчиком.
- **OrderItems** → **Orders**: Каждый элемент заказа связан с одним заказом.
- **OrderItems** → **Products**: Каждый элемент заказа связан с одним товаром.

---

### Шаг 4: Определение первичных и внешних ключей

- Первичные ключи:
  - **Products**: CatalogNum
  - **Customers**: CustomerNum
  - **Orders**: OrderNum
  - **OrderItems**: (OrderNum, CatalogNum)

- Внешние ключи:
  - **Orders**: CustomerNum → Customers.CustomerNum
  - **OrderItems**: OrderNum → Orders.OrderNum, CatalogNum → Products.CatalogNum

---

### Шаг 5: Создание схемы базы данных в SQL

```sql
CREATE TABLE Products (
    CatalogNum INT PRIMARY KEY,
    Product VARCHAR(255) NOT NULL,
    Price DECIMAL(10, 2) NOT NULL
);

CREATE TABLE Customers (
    CustomerNum INT PRIMARY KEY,
    CustomerName VARCHAR(255) NOT NULL,
    CustomerAddress VARCHAR(255) NOT NULL,
    CustomerPhone VARCHAR(20) NOT NULL
);

CREATE TABLE Orders (
    OrderNum INT PRIMARY KEY,
    CustomerNum INT NOT NULL,
    FOREIGN KEY (CustomerNum) REFERENCES Customers(CustomerNum)
);

CREATE TABLE OrderItems (
    OrderNum INT NOT NULL,
    CatalogNum INT NOT NULL,
    Quantity INT NOT NULL,
    PRIMARY KEY (OrderNum, CatalogNum),
    FOREIGN KEY (OrderNum) REFERENCES Orders(OrderNum),
    FOREIGN KEY (CatalogNum) REFERENCES Products(CatalogNum)
);
```

