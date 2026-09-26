# Test Cases — Orders Module (QA Cloud Market API)

Auth: `Authorization: {{apiKey}}` header на каждом запросе.

**Requirement** — ссылка на раздел документации (`https://www.qacloud.dev/market/docs`).
**Based on** — ID кейса из Wiki (`https://www.qacloud.dev/market/wiki#test-cases`), если кейс основан на готовом.

---

### ORD-001 — Оформление заказа с товарами в корзине (happy path)

| Параметр | Значение |
|---|---|
| ID | ORD-001 |
| Priority | High |
| Requirement | Swagger → POST /api/orders |
| Based on | TC-ORD-001 |
| Module | Orders |
| Test data | Корзина с 1 товаром |
| Preconditions | Корзина очищена, затем в неё добавлен товар |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/orders` | `201 Created`, `"message": "Order placed successfully"` |
| 2 | Проверить `order_number` | Соответствует формату `/^O\d{5}$/` |
| 3 | GET корзины | Корзина пуста |

**Actual result:** соответствует ожиданию на всех шагах.
**Status:** Passed

---

### ORD-002 — Оформление заказа с пустой корзиной

| Параметр | Значение |
|---|---|
| ID | ORD-002 |
| Priority | High |
| Requirement | Swagger → POST /api/orders |
| Based on | TC-ORD-002 |
| Module | Orders |
| Test data | Пустая корзина |
| Preconditions | Корзина очищена |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/orders` с пустой корзиной | `400 Bad Request`, `"error": "Basket is empty"` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### ORD-003 — Корректность расчёта total_amount

| Параметр | Значение |
|---|---|
| ID | ORD-003 |
| Priority | High |
| Requirement | Swagger → POST /api/orders (total_amount calculation) |
| Based on | TC-ORD-003 |
| Module | Orders |
| Test data | 2 товара по 6.50 и 2 по 4.50 |
| Preconditions | Известны цены товаров |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Добавить товары в корзину, оформить заказ | `201 Created` |
| 2 | Проверить `total_amount` | Равен сумме (price × quantity) по всем позициям = 22 |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### ORD-004 — price_at_purchase не меняется после изменения цены товара (снепшот)

| Параметр | Значение |
|---|---|
| ID | ORD-004 |
| Priority | High |
| Requirement | Swagger → POST /api/orders (price snapshot) |
| Based on | TC-ORD-004 |
| Module | Orders |
| Test data | Заказ с 1 товаром по цене 6.50, затем цена товара меняется на 10 |
| Preconditions | Товар с известной ценой |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Оформить заказ с товаром по цене 6.50 | `201 Created`, `total_amount: 6.5` |
| 2 | Обновить цену товара в каталоге на 10 (`PUT /api/groceries/:id`) | `200 OK` |
| 3 | GET заказа | `price` в позиции заказа и `total_amount` остаются `6.5`, не пересчитываются на 10 |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### ORD-005 — Остаток на складе уменьшается после оформления заказа

| Параметр | Значение |
|---|---|
| ID | ORD-005 |
| Priority | High |
| Requirement | Swagger → POST /api/orders (stock decrement) |
| Based on | TC-ORD-005 |
| Module | Orders |
| Test data | Товар с известным stock, заказ на N единиц |
| Preconditions | Известен исходный stock товара |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Зафиксировать текущий stock товара | — |
| 2 | Оформить заказ на N единиц этого товара | `201 Created` |
| 3 | Проверить stock товара | Новый stock = исходный − N |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### ORD-006 — Обновление статуса заказа на невалидное значение

| Параметр | Значение |
|---|---|
| ID | ORD-006 |
| Priority | Medium |
| Requirement | Swagger → PUT /api/orders/:id (status enum) |
| Based on | TC-ORD-006 |
| Module | Orders |
| Test data | `{"status": "returned"}` (не входит в допустимый список) |
| Preconditions | Существует заказ со статусом `pending` |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT с `status: "returned"` | `400 Bad Request`, `"error": "Invalid status. Must be one of: pending, processing, shipped, delivered, cancelled"` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### ORD-007 — Формат order_number

| Параметр | Значение |
|---|---|
| ID | ORD-007 |
| Priority | Low |
| Requirement | Swagger → POST /api/orders (order_number format) |
| Based on | TC-ORD-007 |
| Module | Orders |
| Test data | Несколько заказов подряд |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Оформить несколько заказов | У каждого `order_number` соответствует `/^O\d{5}$/` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### ORD-008 — Удаление заказа (сток не восстанавливается)

| Параметр | Значение |
|---|---|
| ID | ORD-008 |
| Priority | Medium |
| Requirement | Swagger → DELETE /api/orders/:id |
| Based on | — |
| Module | Orders |
| Test data | Валидный id существующего заказа |
| Preconditions | Заказ оформлен |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | DELETE `/api/orders/:id` | `200 OK`, `"message": "Order deleted"` |
| 2 | Проверить stock товара из этого заказа | Stock **не** восстанавливается автоматически |

**Actual result:** соответствует ожиданию — заказ удалён, товар в каталоге не пополнился.
**Status:** Passed. 

---

### ORD-009 — Удаление заказа с невалидным форматом id (не-UUID)

| Параметр | Значение |
|---|---|
| ID | ORD-009 |
| Priority | High |
| Requirement | Swagger → DELETE /api/orders/:id (id: uuid) |
| Based on | — |
| Module | Orders |
| Test data | `id = "{20cd71}"` (не соответствует формату UUID) |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | DELETE с id `{20cd71}` | `400 Bad Request`, понятное сообщение о неверном формате id |

**Actual result:** `500 Internal Server Error`, `"error": "invalid input syntax for type uuid: \"{20cd71}\""` — сырая ошибка БД просочилась в ответ.
**Status:** **Failed** → см. BUG-O01

---

### ORD-010 — Обновление статуса с валидным значением, но несуществующим id заказа

| Параметр | Значение |
|---|---|
| ID | ORD-010 |
| Priority | Medium |
| Requirement | Swagger → PUT /api/orders/:id |
| Based on | — |
| Module | Orders |
| Test data | Валидный формат UUID, но несуществующий заказ; `{"status": "shipped"}` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT с валидным статусом на несуществующий (но синтаксически корректный) id | `404 Not Found`, `"error": "Order not found"` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### ORD-011 — Порядок валидации: невалидный статус + невалидный id одновременно

| Параметр | Значение |
|---|---|
| ID | ORD-011 |
| Priority | Low |
| Requirement | Swagger → PUT /api/orders/:id |
| Based on | — |
| Module | Orders |
| Test data | Невалидный `status` (например, `"returned"`) + невалидный/несуществующий id одновременно |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT с невалидным статусом и невалидным id | Уточнить ожидание: приоритет проверки — что важнее, сообщить о плохом статусе или о несуществующем заказе? |

**Actual result:** `400 Bad Request`, `"error": "Invalid status. Must be one of: ..."` — то есть валидация значения `status` выполняется раньше проверки существования заказа по id.
**Status:** Passed.

---

## Сводка найденных дефектов

| ID | Заголовок | Severity | Связанные кейсы |
|---|---|---|---|
| BUG-O01 | Невалидный формат id (не-UUID) на DELETE /api/orders/:id вызывает 500 вместо 400 | Major | ORD-009 |
