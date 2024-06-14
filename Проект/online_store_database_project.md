# Проект базы данных для интернет-магазина

## 1. Предметная область

Интернет-магазин продает различные товары. Каждый товар имеет уникальный идентификатор, название, описание, цену и категорию. Магазин также имеет клиентов, каждый из которых имеет уникальный идентификатор, имя, адрес и контактные данные. Клиенты могут делать заказы, и каждый заказ содержит информацию о клиенте, дате заказа и статусе заказа. Заказ состоит из нескольких позиций, каждая из которых указывает на товар, его количество и цену на момент заказа.

## 2. Нормализация базы данных

**1-я нормальная форма (1НФ):**
Все атрибуты атомарны.

**2-я нормальная форма (2НФ):**
Все неключевые атрибуты зависят от всего первичного ключа.

**3-я нормальная форма (3НФ):**
Все неключевые атрибуты зависят только от первичного ключа.

### Таблицы:

**Customers (Клиенты):**
- customer_id (PK)
- name
- address
- contact_info

**Products (Товары):**
- product_id (PK)
- name
- description
- price
- category

**Orders (Заказы):**
- order_id (PK)
- customer_id (FK)
- order_date
- status

**OrderItems (Позиции заказов):**
- order_item_id (PK)
- order_id (FK)
- product_id (FK)
- quantity
- price

## 3. ER-диаграмма (IDEF1X)

```plaintext
  +------------------+            +------------------+            +------------------+            +------------------+
  |     Customers    |            |     Products     |            |      Orders      |            |    OrderItems    |
  +------------------+            +------------------+            +------------------+            +------------------+
  | customer_id (PK) |            | product_id (PK)  |            | order_id (PK)    |            | order_item_id (PK)|
  | name             |            | name             |            | customer_id (FK) |            | order_id (FK)     |
  | address          |            | description      |            | order_date       |            | product_id (FK)   |
  | contact_info     |            | price            |            | status           |            | quantity          |
  +------------------+            | category         |            +------------------+            | price             |
                                  +------------------+                                            +------------------+
```

## 4. Создание схемы базы данных

```sql
CREATE DATABASE online_store;
USE online_store;

CREATE TABLE Customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    address VARCHAR(255) NOT NULL,
    contact_info VARCHAR(255) NOT NULL
);

CREATE TABLE Products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    category VARCHAR(255) NOT NULL
);

CREATE TABLE Orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date DATE NOT NULL,
    status VARCHAR(50) NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);

CREATE TABLE OrderItems (
    order_item_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (product_id) REFERENCES Products(product_id)
);
```

## 5. Создание индексов

```sql
CREATE INDEX idx_customer_id ON Orders (customer_id);
CREATE INDEX idx_order_date ON Orders (order_date);
CREATE INDEX idx_product_id ON OrderItems (product_id);
```

## 6. Триггеры, функции и процедуры

### Процедура регистрации нового товара

```sql
DELIMITER //

CREATE PROCEDURE RegisterNewProduct(
    IN p_name VARCHAR(255),
    IN p_description TEXT,
    IN p_price DECIMAL(10,2),
    IN p_category VARCHAR(255)
)
BEGIN
    INSERT INTO Products (name, description, price, category)
    VALUES (p_name, p_description, p_price, p_category);
END //

DELIMITER ;
```

### Функция получения остатка по складу

```sql
DELIMITER //

CREATE FUNCTION GetStock(p_product_id INT) RETURNS INT
BEGIN
    DECLARE stock INT;
    SELECT SUM(quantity) INTO stock
    FROM OrderItems
    WHERE product_id = p_product_id;
    RETURN stock;
END //

DELIMITER ;
```

### Пример триггера

```sql
CREATE TABLE ProductStock (
    product_id INT PRIMARY KEY,
    stock INT NOT NULL,
    FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

DELIMITER //

CREATE TRIGGER after_order_item_insert
AFTER INSERT ON OrderItems
FOR EACH ROW
BEGIN
    DECLARE current_stock INT;
    
    SELECT stock INTO current_stock
    FROM ProductStock
    WHERE product_id = NEW.product_id;
    
    IF current_stock IS NULL THEN
        INSERT INTO ProductStock (product_id, stock)
        VALUES (NEW.product_id, NEW.quantity);
    ELSE
        UPDATE ProductStock
        SET stock = stock + NEW.quantity
        WHERE product_id = NEW.product_id;
    END IF;
END //

DELIMITER ;
```

## 7. Оценка использования NoSQL баз данных

### Преимущества NoSQL

- Гибкость схемы данных.
- Высокая производительность при чтении и записи данных.
- Возможность горизонтального масштабирования.

### Недостатки NoSQL

- Отсутствие поддержки сложных транзакций.
- Меньшая зрелость в плане стандартов и инструментов по сравнению с реляционными СУБД.
- Ограниченные возможности для сложных запросов и анализа данных.

Для нашего интернет-магазина, где важна консистентность данных и поддержка сложных транзакций, реляционная база данных (например, MySQL или PostgreSQL) является более подходящим выбором. Однако, если потребуется обрабатывать большие объемы неструктурированных данных или обеспечить высокую производительность при масштабировании, можно рассмотреть использование NoSQL баз данных.
