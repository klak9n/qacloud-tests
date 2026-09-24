# Test Cases — Products Module (QA Cloud Market API)

Auth: `Authorization: {{apiKey}}` header на каждом запросе, если не указано иное.

**Requirement** — ссылка на раздел спецификации API (Swagger), откуда взято ожидаемое поведение: `https://www.qacloud.dev/market/docs`
**Based on** — если кейс основан на готовом тест-кейсе из Wiki (`https://www.qacloud.dev/market/wiki#test-cases`), здесь указан его ID. Поле отсутствует, если кейс придуман самостоятельно.

---

## GET /api/groceries

### PROD-001 — Получение списка всех товаров

| Параметр | Значение |
|---|---|
| ID | PROD-001 |
| Priority | High |
| Requirement | Swagger → GET /api/groceries |
| Based on | TC-PROD-001 |
| Module | Products |
| Test data | Отсутствуют |
| Preconditions | Пользователь зарегистрирован, каталог засеян дефолтными товарами |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Отправить GET `/api/groceries` без query-параметров | Статус `200 OK` |
| 2 | Проверить тело ответа | `{ "products": [...] }`, массив содержит ≥ 1 элемент |
| 3 | Проверить структуру объекта товара | Каждый объект содержит: `id`, `product_name`, `price`, `category`, `stock`, `temperature_zone`, `weighted` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-002 — Сортировка списка товаров

| Параметр | Значение |
|---|---|
| ID | PROD-002 |
| Priority | Medium |
| Requirement | Swagger → GET /api/groceries (query param sort) |
| Based on | — |
| Module | Products |
| Test data | `sort=asc`, `sort=desc` |
| Preconditions | В каталоге есть товары с разными первыми символами имени |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Отправить GET `/api/groceries?sort=asc` | `200 OK`, товары отсортированы по `product_name` A→Z |
| 2 | Отправить GET `/api/groceries?sort=desc` | `200 OK`, товары отсортированы по `product_name` Z→A |

**Actual result:** сортировка соблюдается, товары с числовыми названиями идут первыми при `asc`.
**Status:** Passed 

---

## GET /api/groceries/filter

### PROD-003 — Фильтр по temperature_zone (валидное значение)

| Параметр | Значение |
|---|---|
| ID | PROD-003 |
| Priority | High |
| Requirement | Swagger → GET /api/groceries/filter (param temperature_zone) |
| Based on | — |
| Module | Products |
| Test data | `temperature_zone=Chilled` (и остальные допустимые значения) |
| Preconditions | В каталоге есть товары с разными temperature_zone |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries/filter?temperature_zone=Chilled` | `200 OK`, все товары в ответе имеют `temperature_zone: "Chilled"` |
| 2 | Повторить для `Dry`, `Frozen`, `Room Temperature` | Аналогично, только соответствующие товары |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-004 — Фильтр по weighted (валидное значение)

| Параметр | Значение |
|---|---|
| ID | PROD-004 |
| Priority | Medium |
| Requirement | Swagger → GET /api/groceries/filter (param weighted) |
| Based on | — |
| Module | Products |
| Test data | `weighted=true`, `weighted=false` |
| Preconditions | В каталоге есть товары с обоими значениями weighted |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries/filter?weighted=true` | `200 OK`, все товары в ответе имеют `weighted: true` |
| 2 | GET `/api/groceries/filter?weighted=false` | `200 OK`, все товары в ответе имеют `weighted: false` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-005 — Фильтр по нескольким категориям через запятую

| Параметр | Значение |
|---|---|
| ID | PROD-005 |
| Priority | High |
| Requirement | Swagger → GET /api/groceries/filter (param category, comma-separated) |
| Based on | TC-PROD-004 |
| Module | Products |
| Test data | `category=Dairy,Meat` |
| Preconditions | В документации заявлена категория `Meat` |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries/filter?category=Dairy,Meat` | `200 OK`, возвращены товары категорий Dairy и Meat |

**Actual result:** возвращён пустой список `[]`. Реальные категории в данных — `meat-seafood` и др. подкатегории, а не `Meat`.
**Status:** **Failed** → см. BUG-004 

---

### PROD-006 — Фильтр по фактической подкатегории (уточняющий кейс к PROD-005)

| Параметр | Значение |
|---|---|
| ID | PROD-006 |
| Priority | Medium |
| Requirement | Swagger → GET /api/groceries/filter (param category) |
| Based on | — |
| Module | Products |
| Test data | `category=meat-seafood` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries/filter?category=meat-seafood` | `200 OK`, возвращены товары этой подкатегории |

**Actual result:** соответствует. Фильтр технически исправен, работает по фактическому названию категории в БД.
**Status:** Passed 

---

### PROD-007 — Фильтр без параметров

| Параметр | Значение |
|---|---|
| ID | PROD-007 |
| Priority | Low |
| Requirement | Swagger → GET /api/groceries/filter |
| Based on | — |
| Module | Products |
| Test data | Без query-параметров |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries/filter` без параметров | `200 OK`, возвращены все товары |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-008 — Фильтр с пустыми значениями параметров

| Параметр | Значение |
|---|---|
| ID | PROD-008 |
| Priority | Low |
| Requirement | Swagger → GET /api/groceries/filter |
| Based on | — |
| Module | Products |
| Test data | `category=`, `temperature_zone=` (пустые строки) |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries/filter?category=&temperature_zone=` | `200 OK`, возвращены все товары (пустой параметр игнорируется) |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-009 — Фильтр weighted с пустым значением

| Параметр | Значение |
|---|---|
| ID | PROD-009 |
| Priority | Medium |
| Requirement | Swagger → GET /api/groceries/filter (param weighted) |
| Based on | — |
| Module | Products |
| Test data | `weighted=` (пустое значение) |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries/filter?weighted=` | `400 Bad Request`, `"error": "weighted parameter must be \"true\" or \"false\""` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-010 — Фильтр temperature_zone с невалидным значением

| Параметр | Значение |
|---|---|
| ID | PROD-010 |
| Priority | Medium |
| Requirement | Swagger → GET /api/groceries/filter (param temperature_zone) |
| Based on | — |
| Module | Products |
| Test data | `temperature_zone=Warm` (не входит в допустимый список) |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries/filter?temperature_zone=Warm` | `400 Bad Request`, `"error": "temperature_zone must be one of: Dry, Frozen, Chilled, Room Temperature"` |
| 2 | GET `/api/groceries/filter?temperature_zone=Chilled,Warm` | `400 Bad Request` |

**Actual result:** соответствует ожиданию по обоим шагам.
**Status:** Passed

---

### PROD-011 — Фильтр category по произвольной строке (свободное поле)

| Параметр | Значение |
|---|---|
| ID | PROD-011 |
| Priority | Medium |
| Requirement | Wiki → Description → UI Overview → Products Tab ("...and any custom categories you create") |
| Based on | — |
| Module | Products |
| Test data | Спецсимволы, кириллица, пробелы, случайный набор букв |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries/filter?category=!@#$` | `200 OK`, `[]` |
| 2 | GET `/api/groceries/filter?category=молоко` | Аналогично п.1 |
| 3 | GET `/api/groceries/filter?category=asdkfj` | Аналогично п.1 |

**Actual result:** во всех случаях `200 OK`, `[]`.
**Status:** Passed. 

---

## POST /api/groceries

### PROD-012 — Создание товара с полным набором полей

| Параметр | Значение |
|---|---|
| ID | PROD-012 |
| Priority | High |
| Requirement | Swagger → POST /api/groceries |
| Based on | TC-PROD-002 |
| Module | Products |
| Test data | `{"name":"Milk","price":4.99,"category":"Dairy","temperature_zone":"Chilled","weighted":false,"details":{"brand":"DairyFresh","volume":"1L"}}` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` с валидным телом | `201 Created` |
| 2 | Проверить тело ответа | Присутствует `id`; `product_name`, `price`, `category` и остальные отправленные поля соответствуют отправленным |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-013 — Создание товара без обязательного поля name

| Параметр | Значение |
|---|---|
| ID | PROD-013 |
| Priority | High |
| Requirement | Swagger → POST /api/groceries (required fields) |
| Based on | — |
| Module | Products |
| Test data | Тело без поля `name`/`product_name` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` без `name` | `400 Bad Request`, `"error": "product_name/name, price, and category are required"` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-014 — Создание товара без обязательного поля price

| Параметр | Значение |
|---|---|
| ID | PROD-014 |
| Priority | High |
| Requirement | Swagger → POST /api/groceries (required fields) |
| Based on | — |
| Module | Products |
| Test data | Тело без поля `price` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` без `price` | `400 Bad Request` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-015 — Создание товара без обязательного поля category

| Параметр | Значение |
|---|---|
| ID | PROD-015 |
| Priority | High |
| Requirement | Swagger → POST /api/groceries (required fields) |
| Based on | — |
| Module | Products |
| Test data | Тело без поля `category` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` без `category` | `400 Bad Request` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-016 — Создание товара без всех обязательных полей

| Параметр | Значение |
|---|---|
| ID | PROD-016 |
| Priority | Medium |
| Requirement | Swagger → POST /api/groceries (required fields) |
| Based on | — |
| Module | Products |
| Test data | Пустое тело `{}` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` с телом `{}` | `400 Bad Request` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### PROD-017 — Создание товара с price = 0

| Параметр | Значение |
|---|---|
| ID | PROD-017 |
| Priority | High |
| Requirement | Swagger → POST /api/groceries (price: positive number) |
| Based on | — |
| Module | Products |
| Test data | `price: 0` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` с `price: 0`, остальные поля валидны | `400 Bad Request` с сообщением, указывающим на некорректное значение price |

**Actual result:** `400 Bad Request`, но текст ошибки `"product_name/name, price, and category are required"` вводит в заблуждение — поле `price` присутствует в запросе.
**Status:** **Failed** → см. BUG-003

---

### PROD-018 — Создание товара с отрицательным price

| Параметр | Значение |
|---|---|
| ID | PROD-018 |
| Priority | High |
| Requirement | Swagger → POST /api/groceries (price: positive number) |
| Based on | — |
| Module | Products |
| Test data | `price: -10` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` с `price: -10`, остальные поля валидны | `400 Bad Request` — цена не может быть отрицательной |

**Actual result:** `201 Created`, товар создан с `price: -10`. Подтверждено повторным GET по id.
**Status:** **Failed** → см. BUG-001

---

### PROD-019 — Создание товара с нечисловым price

| Параметр | Значение |
|---|---|
| ID | PROD-019 |
| Priority | Medium |
| Requirement | Swagger → POST /api/groceries (price: number) |
| Based on | — |
| Module | Products |
| Test data | `price: "asf"` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` с `price: "asf"` | `400 Bad Request` |

**Actual result:** `400 Bad Request`, `"error": "invalid input syntax for type numeric: \"asf\""`.
**Status:** Passed

---

### PROD-020 — Создание товара с произвольной (не из списка) категорией

| Параметр | Значение |
|---|---|
| ID | PROD-020 |
| Priority | Low |
| Requirement | Wiki → Description → UI Overview → Products Tab ("...and any custom categories you create") |
| Based on | — |
| Module | Products |
| Test data | `category: "Warm"` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` с `category: "Warm"` | `201 Created` — category свободное поле, пользователь может задавать собственные категории |

**Actual result:** `201 Created`, товар создан с произвольной категорией `"Warm"`. 
**Status:** Passed

---

### PROD-021 — Создание товара с невалидным temperature_zone

| Параметр | Значение |
|---|---|
| ID | PROD-021 |
| Priority | High |
| Requirement | Swagger → POST /api/groceries (temperature_zone enum) |
| Based on | TC-PROD-003 |
| Module | Products |
| Test data | `temperature_zone: "Warm"` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | POST `/api/groceries` с `temperature_zone: "Warm"` | `400 Bad Request`, `"error": "temperature_zone must be one of: Dry, Frozen, Chilled, Room Temperature"` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

## PUT /api/groceries/:id

### PROD-022 — Частичное обновление (только price)

| Параметр | Значение |
|---|---|
| ID | PROD-022 |
| Priority | High |
| Requirement | Swagger → PUT /api/groceries/:id (partial update) |
| Based on | TC-PROD-007 |
| Module | Products |
| Test data | Валидный существующий `id`, тело `{"price": <новое значение>}` |
| Preconditions | Товар создан заранее |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT `/api/groceries/:id` с телом `{"price": X}` | `200 OK` |
| 2 | GET `/api/groceries/:id` | `price` обновлён, остальные поля не изменились |
| 3 | Проверить отображение на UI (`/market/<username>`) | Цена обновлена в карточке товара |

**Actual result:** соответствует ожиданию на всех шагах.
**Status:** Passed

---

### PROD-023 — Обновление с невалидным форматом id (не-UUID)

| Параметр | Значение |
|---|---|
| ID | PROD-023 |
| Priority | Medium |
| Requirement | Swagger → PUT /api/groceries/:id (path param id: uuid) |
| Based on | — |
| Module | Products |
| Test data | `id = "123"`, `id = "{239b}"` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT `/api/groceries/123` с валидным телом | `400 Bad Request`, `"error": "invalid input syntax for type uuid: \"123\""` |
| 2 | Повторить с другим не-UUID значением id | Аналогичное поведение — `400` |

**Actual result:** соответствует ожиданию, поведение стабильно на разных невалидных значениях.
**Status:** Passed

---

### PROD-024 — Обновление price = 0

| Параметр | Значение |
|---|---|
| ID | PROD-024 |
| Priority | High |
| Requirement | Swagger → PUT /api/groceries/:id (price: positive number) |
| Based on | — |
| Module | Products |
| Test data | Валидный `id`, `{"price": 0}` |
| Preconditions | Товар создан заранее |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT `/api/groceries/:id` с `{"price": 0}` | По аналогии с POST (PROD-017) ожидается `400 Bad Request` |

**Actual result:** `200 OK`, цена реально обновляется на 0 (подтверждено на UI и повторным GET).
**Status:** **Failed** → см. BUG-002 

---

### PROD-025 — Обновление price отрицательным значением

| Параметр | Значение |
|---|---|
| ID | PROD-025 |
| Priority | High |
| Requirement | Swagger → PUT /api/groceries/:id (price: positive number) |
| Based on | — |
| Module | Products |
| Test data | Валидный `id`, `{"price": -24}` |
| Preconditions | Товар создан заранее |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT `/api/groceries/:id` с `{"price": -24}` | `400 Bad Request` |

**Actual result:** `200 OK`, товар обновлён с отрицательной ценой.
**Status:** **Failed** → см. BUG-001 

---

### PROD-026 — Обновление price нечисловым/некорректным значением

| Параметр | Значение |
|---|---|
| ID | PROD-026 |
| Priority | Low |
| Requirement | Swagger → PUT /api/groceries/:id (price: number) |
| Based on | — |
| Module | Products |
| Test data | `{"price": "fsf"}`, `{"price": "1$"}`, `{"price": 1$}` |
| Preconditions | Товар создан заранее |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | PUT `/api/groceries/:id` с каждым из значений по очереди | `400 Bad Request` во всех случаях |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

## DELETE /api/groceries/:id

### PROD-027 — Удаление товара по валидному id

| Параметр | Значение |
|---|---|
| ID | PROD-027 |
| Priority | High |
| Requirement | Swagger → DELETE /api/groceries/:id |
| Based on | — |
| Module | Products |
| Test data | Валидный `id` ранее созданного товара |
| Preconditions | Товар создан заранее |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | DELETE `/api/groceries/:id` | `200 OK`, `"message": "Deleted"` |
| 2 | GET `/api/groceries/:id` | `404 Not Found`, `"error": "Item not found"` |
| 3 | Проверить UI | Товар исчезает из каталога |

**Actual result:** соответствует ожиданию на всех шагах.
**Status:** Passed

---

### PROD-028 — Удаление товара по несуществующему/невалидному id

| Параметр | Значение |
|---|---|
| ID | PROD-028 |
| Priority | Medium |
| Requirement | Swagger → DELETE /api/groceries/:id |
| Based on | — |
| Module | Products |
| Test data | Несуществующий/невалидный `id` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | DELETE `/api/groceries/:id` с несуществующим id | `404 Not Found` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

## Авторизация

### PROD-029 — Запрос с невалидным API-ключом

| Параметр | Значение |
|---|---|
| ID | PROD-029 |
| Priority | High |
| Requirement | Swagger → Authorization header |
| Based on | TC-PROD-005 |
| Module | Products / Auth |
| Test data | Невалидное значение ключа в заголовке `Authorization` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | GET `/api/groceries` с невалидным ключом в заголовке `Authorization` | `401 Unauthorized`, `"error": "Invalid API Key."` |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

## Сводка найденных дефектов

| ID | Заголовок | Severity | Связанные кейсы |
|---|---|---|---|
| BUG-001 | POST и PUT /api/groceries принимают отрицательное значение price | Major | PROD-018, PROD-025 |
| BUG-002 | Несогласованная валидация price = 0 между POST (400) и PUT (200) | Major | PROD-017, PROD-024 |
| BUG-003 | Сообщение об ошибке при price = 0 вводит в заблуждение | Minor | PROD-017 |
| BUG-004 | BUG-004 — Категория Meat, заявленная в документации, отсутствует в дефолтном каталоге | Major | PROD-005, PROD-006 |
