# Test Cases — Basket Module (QA Cloud Market API)

Auth: `Authorization: {{apiKey}}` header на каждом запросе.

**Requirement** — ссылка на раздел документации (`https://www.qacloud.dev/market/docs`), откуда взято ожидаемое поведение.
**Based on** — ID кейса из Wiki (`https://www.qacloud.dev/market/wiki#test-cases`), если кейс основан на готовом.

---

### BASK-001 — Добавление товара в корзину (happy path)

| Параметр | Значение |
|---|---|
| ID | BASK-001 |
| Priority | High |
| Requirement | Swagger → POST /api/basket |
| Based on | TC-BASK-001 |
| Module | Basket |
| Test data | `{"product_id": "<валидный id>", "quantity": 2}` |
| Preconditions | Товар существует, есть в наличии |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/basket` с валидными `product_id` и `quantity: 2` | `201 Created`, `"message": "Item added to basket"` |
| 2 | GET корзины | Товар присутствует, `quantity: 2` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### BASK-002 — Повторное добавление того же товара объединяет количество

| Параметр | Значение |
|---|---|
| ID | BASK-002 |
| Priority | High |
| Requirement | Swagger → POST /api/basket |
| Based on | TC-BASK-002 |
| Module | Basket |
| Test data | Дважды `{"product_id": "<тот же id>", "quantity": 2}` |
| Preconditions | Товар доступен в достаточном количестве |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `product_id: X, quantity: 2` | `201 Created`, одна запись, `quantity: 2` |
| 2 | Повторить POST с тем же `product_id: X, quantity: 2` | `200 OK`, `"message": "Basket item updated"`, `quantity` объединяется в одну запись (4), а не создаёт вторую |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### BASK-003 — Добавление сверх остатка с учётом уже лежащего в корзине

| Параметр | Значение |
|---|---|
| ID | BASK-003 |
| Priority | High |
| Requirement | Swagger → POST /api/basket (stock validation, cumulative) |
| Based on | TC-BASK-003, TC-BASK-004 |
| Module | Basket |
| Test data | Товар с `stock: 10`. Добавить `quantity: 5`, затем ещё `quantity: 6` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `quantity: 5` | `201 Created` |
| 2 | POST `quantity: 6` (5+6=11 > 10) | `400 Bad Request`, `"error": "Not enough stock. Available: 10, Already in basket: 5"` — валидация учитывает то, что уже в корзине |

**Actual result:** соответствует ожиданию на обоих шагах.
**Status:** Passed

---

### BASK-004 — Добавление с quantity = 0

| Параметр | Значение |
|---|---|
| ID | BASK-004 |
| Priority | High |
| Requirement | Swagger → POST /api/basket (quantity: minimum 1) |
| Based on | TC-BASK-005 |
| Module | Basket |
| Test data | `{"product_id": "<id>", "quantity": 0}` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST с `quantity: 0` | `400 Bad Request`, `"error": "quantity must be at least 1"` |

**Actual result:** `400 Bad Request`, но сообщение `"error": "product_id and quantity are required"` — как будто поле отсутствует, хотя оно передано.
**Status:** **Failed** → см. BUG-B01

---

### BASK-005 — Добавление с нечисловым quantity

| Параметр | Значение |
|---|---|
| ID | BASK-005 |
| Priority | High |
| Requirement | Swagger → POST /api/basket (quantity: integer) |
| Based on | — |
| Module | Basket |
| Test data | `{"product_id": "<id>", "quantity": "asf"}` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST с `quantity: "asf"` | `400 Bad Request` с понятным сообщением о типе |

**Actual result:** `500 Internal Server Error`, `"error": "invalid input syntax for type integer: \"...\""` — сырая ошибка БД в ответе клиенту.
**Status:** **Failed** → см. BUG-B02

---

### BASK-006 — Каскадное удаление при удалении товара из каталога

| Параметр | Значение |
|---|---|
| ID | BASK-006 |
| Priority | High |
| Requirement | Swagger → DELETE /api/groceries/:id (cascade behavior) |
| Based on | TC-BASK-006 |
| Module | Basket |
| Test data | Товар добавлен в корзину, затем удалён из каталога |
| Preconditions | Корзина очищена перед тестом |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Добавить 2 товара в корзину | `201 Created` для обоих |
| 2 | Удалить один товар из каталога (`DELETE /api/groceries/:id`) | `200 OK` |
| 3 | GET корзины | Удалённый товар отсутствует |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### BASK-007 — Обновление количества (happy path)

| Параметр | Значение |
|---|---|
| ID | BASK-007 |
| Priority | High |
| Requirement | Swagger → PUT /api/basket/:id |
| Based on | — |
| Module | Basket |
| Test data | Валидный id позиции корзины, новое `quantity` |
| Preconditions | Товар уже в корзине |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT с новым валидным `quantity` | `200 OK` |
| 2 | GET корзины | Новое значение отображается |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### BASK-008 — Обновление нечисловым значением quantity

| Параметр | Значение |
|---|---|
| ID | BASK-008 |
| Priority | High |
| Requirement | Swagger → PUT /api/basket/:id (quantity: integer) |
| Based on | — |
| Module | Basket |
| Test data | `{"quantity": "asd"}` |
| Preconditions | Товар уже в корзине |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT с `quantity: "asd"` | `400 Bad Request` с понятным сообщением |

**Actual result:** `500 Internal Server Error`, `"error": "invalid input syntax for type integer: \"asd\""`.
**Status:** **Failed** → см. BUG-B02 

---

### BASK-009 — Удаление товара из корзины (happy path + повтор)

| Параметр | Значение |
|---|---|
| ID | BASK-009 |
| Priority | High |
| Requirement | Swagger → DELETE /api/basket/:id |
| Based on | — |
| Module | Basket |
| Test data | Валидный id позиции корзины |
| Preconditions | Товар в корзине |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | DELETE с валидным id | `200 OK`, `"message": "Item removed from basket"` |
| 2 | Повторить DELETE с тем же id | `404 Not Found`, `"error": "Basket item not found"` |

**Actual result:** соответствует ожиданию на обоих шагах.
**Status:** Passed

---

### BASK-010 — Удаление по невалидному (не-UUID) id

| Параметр | Значение |
|---|---|
| ID | BASK-010 |
| Priority | High |
| Requirement | Swagger → DELETE /api/basket/:id (id: uuid) |
| Based on | — |
| Module | Basket |
| Test data | `id = "{166}"` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | DELETE с id `{166}` | `400 Bad Request`, понятное сообщение о формате id |

**Actual result:** `500 Internal Server Error`, `"error": "invalid input syntax for type uuid: \"{166}\""`.
**Status:** **Failed** → см. BUG-B02 

---

## Сводка найденных дефектов

| ID | Заголовок | Severity | Связанные кейсы |
|---|---|---|---|
| BUG-B01 | quantity = 0 возвращает "required" вместо "must be at least 1" | Minor | BASK-004 |
| BUG-B02 | Невалидный тип quantity/id вызывает 500 с сырой ошибкой БД вместо 400 | Major | BASK-005, BASK-008, BASK-010 |
