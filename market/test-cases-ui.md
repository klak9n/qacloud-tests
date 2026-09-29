# Test Cases — UI (QA Cloud Market)

**Requirement** — ссылка на Wiki (`https://www.qacloud.dev/market/wiki#test-cases`) или наблюдаемое поведение UI.
**Based on** — ID кейса из Wiki, если кейс основан на готовом.

---

### UI-001 — Счётчики корзины после добавления товара (уточняющий кейс)

| Параметр | Значение |
|---|---|
| ID | UI-001 |
| Priority | Medium |
| Requirement | Wiki UI Overview → Stats Dashboard |
| Based on | TC-UI-001 |
| Module | UI / Stats Dashboard |
| Test data | Добавить 1 товар с quantity: 3 |
| Preconditions | Корзина пуста |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Зафиксировать значения счётчика в шапке (иконка Basket) и стата "Basket Units" на дашборде | Оба равны 0 |
| 2 | Добавить 1 товар с `quantity: 3` | Счётчик в шапке (Basket) увеличивается на **1** (по числу уникальных товаров); стат "Basket Units" на дашборде увеличивается на **3** (по сумме quantity) |

**Actual result:** в приложении фактически два разных счётчика с разной семантикой: значок в шапке считает количество уникальных товарных позиций, а "Basket Units" на дашборде — суммарное количество единиц товара.
**Status:** Passed по факту поведения, но исходная формулировка `TC-UI-001` в Wiki (единый "Basket Items counter", ожидаемое +3) не соответствует реальному UI, где нет элемента с названием "Basket Items" — есть два разных элемента с разными названиями и разной логикой подсчёта. → см. BUG-UI-01 (документация не соответствует интерфейсу)

---

### UI-002 — Обновление статистики после оформления заказа

| Параметр | Значение |
|---|---|
| ID | UI-002 |
| Priority | High |
| Requirement | Wiki UI Overview → Stats Dashboard |
| Based on | TC-UI-002 |
| Module | UI / Stats Dashboard |
| Test data | — |
| Preconditions | В корзине есть товары |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Зафиксировать Orders и Basket Units | — |
| 2 | Нажать PLACE ORDER | Orders увеличивается на 1, Basket Units становится 0 |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### UI-003 — Inventory Value не зависит от состояния корзины

| Параметр | Значение |
|---|---|
| ID | UI-003 |
| Priority | Medium |
| Requirement | Wiki UI Overview → Stats Dashboard |
| Based on | TC-UI-003 |
| Module | UI / Stats Dashboard |
| Test data | — |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Зафиксировать Inventory Value | — |
| 2 | Полностью очистить корзину | Inventory Value не меняется — считается от каталога, а не от корзины/заказов |

**Actual result:** соответствует ожиданию ($2174.31 до и после).
**Status:** Passed

---

### UI-004 — Кнопка Reset восстанавливает удалённые товары

| Параметр | Значение |
|---|---|
| ID | UI-004 |
| Priority | Medium |
| Requirement | Wiki UI Overview → "Reset (top navigation bar)" |
| Based on | TC-UI-007 |
| Module | UI / Products |
| Test data | — |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Удалить товар через UI | Товар пропадает из каталога, счётчик Products уменьшается |
| 2 | Найти и нажать Reset **в верхней навигационной панели** | Товар восстанавливается, счётчик Products возвращается к исходному значению |

**Actual result:** сама функция сброса работает корректно (шаг вызывает нужный эффект), но кнопки Reset в верхней навигации не существует — она находится внутри раздела Data Viewer.
**Status:** **Failed** (по описанию местоположения) → см. BUG-UI-02. Функциональность как таковая работает, дефект — в несоответствии документации реальному расположению элемента.

---

### UI-005 — Сообщение о пустой корзине

| Параметр | Значение |
|---|---|
| ID | UI-005 |
| Priority | Low |
| Requirement | Wiki UI Overview → Basket Tab |
| Based on | TC-UI-009 |
| Module | UI / Basket |
| Test data | — |
| Preconditions | Корзина пуста |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Открыть вкладку Basket с пустой корзиной | Показана иконка корзины и текст: "Your basket is empty. Start marketing!" |

**Actual result:** текст отображается с опечаткой: **"Start marketping!"** вместо "Start marketing!".
**Status:** **Failed** → см. BUG-UI-03

---

### UI-006 — Создание товара с обязательными полями (happy path)

| Параметр | Значение |
|---|---|
| ID | UI-006 |
| Priority | High |
| Requirement | Wiki UI Overview → Products Tab → Add a Product |
| Based on | — |
| Module | UI / Products |
| Test data | Валидные Product Name, Price, Category |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Нажать "Add a Product" | Открывается форма |
| 2 | Заполнить обязательные поля валидными значениями | — |
| 3 | Нажать кнопку создания | Товар создаётся |
| 4 | Найти товар в каталоге | Карточка содержит введённые данные |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### UI-007 — Создание товара с price = 0 (UI)

| Параметр | Значение |
|---|---|
| ID | UI-007 |
| Priority | High |
| Requirement | Wiki UI Overview → Add a Product (валидация полей) |
| Based on | — |
| Module | UI / Products |
| Test data | `price = 0`, остальные поля валидны |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Заполнить форму с `price: 0`, нажать создание | Либо товар создаётся, либо форма показывает понятное сообщение об ошибке ("цена должна быть больше 0") |

**Actual result:** товар **не создаётся**, но никакого сообщения об ошибке не появляется — пользователь не получает никакой обратной связи о причине неудачи.
**Status:** **Failed** → см. BUG-UI-04

---

### UI-008 — Создание товара с отрицательной ценой (UI) — сверка с API

| Параметр | Значение |
|---|---|
| ID | UI-008 |
| Priority | High |
| Requirement | Wiki UI Overview → Add a Product (валидация полей) |
| Based on | — |
| Module | UI / Products |
| Test data | `price = -4` |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Ввести `price: -4` в форму | Форма показывает валидационное сообщение и не даёт отправить |

**Actual result:** форма корректно показывает сообщение "пожалуйста, выберите значение не менее 0" и блокирует отправку.
**Status:** Passed на уровне UI. 

---

### UI-009 — Категория товара ограничена дефолтным списком в UI

| Параметр | Значение |
|---|---|
| ID | UI-009 |
| Priority | Low |
| Requirement | Wiki UI Overview → Add a Product (Category field) |
| Based on | — |
| Module | UI / Products |
| Test data | — |
| Preconditions | — |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Открыть dropdown категории в форме создания товара | Уточнить у продукта: должен ли пользователь иметь возможность ввести свою категорию через UI? |

**Actual result:** dropdown содержит только базовые (дефолтные) категории, свободный ввод произвольной категории через UI недоступен — при том, что сам API (`POST /api/groceries`) принимает любую строку в поле `category` (см. PROD-020).
**Status:** Passed 

---

### UI-010 — Степпер количества обновляет subtotal в реальном времени

| Параметр | Значение |
|---|---|
| ID | UI-010 |
| Priority | High |
| Requirement | Wiki UI Overview → Basket Tab |
| Based on | TC-UI-008 |
| Module | UI / Basket |
| Test data | Товар $6.50, qty 3 → 4 |
| Preconditions | Товар в корзине |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Нажать "+" у товара в корзине | Subtotal и Order Summary обновляются мгновенно ($19.50 → $26.00) |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

### UI-011 — Кнопка PLACE ORDER отсутствует при пустой корзине

| Параметр | Значение |
|---|---|
| ID | UI-011 |
| Priority | Medium |
| Requirement | Wiki UI Overview → Basket Tab |
| Based on | TC-UI-011 |
| Module | UI / Basket |
| Test data | — |
| Preconditions | Корзина пуста |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Открыть Basket с пустой корзиной | Кнопка PLACE ORDER отсутствует или неактивна |

**Actual result:** кнопка отсутствует.
**Status:** Passed

---

### UI-012 — Смена статуса заказа недоступна после DELIVERED

| Параметр | Значение |
|---|---|
| ID | UI-012 |
| Priority | Medium |
| Requirement | Wiki UI Overview → Orders Tab (status badge) |
| Based on | TC-UI-014 |
| Module | UI / Orders |
| Test data | Заказ: pending → delivered |
| Preconditions | Заказ создан |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Изменить статус заказа на DELIVERED | Цвет бейджа меняется на зелёный |
| 2 | Попробовать изменить статус ещё раз | Статус не меняется |

**Actual result:** после установки статуса DELIVERED элементы управления сменой статуса в UI становятся недоступны.
**Status:** Passed 

---

### UI-013 — DELETE ORDER удаляет заказ и уменьшает счётчик

| Параметр | Значение |
|---|---|
| ID | UI-013 |
| Priority | Medium |
| Requirement | Wiki UI Overview → Orders Tab |
| Based on | TC-UI-017 |
| Module | UI / Orders |
| Test data | — |
| Preconditions | Существует 1 заказ |

**Steps**

| # | Шаг | Ожидаемый результат |
|---|---|---|
| 1 | Нажать DELETE ORDER | Заказ пропадает из списка, счётчик Orders уменьшается до 0 |

**Actual result:** соответствует ожиданию.
**Status:** Passed

---

## Сводка найденных дефектов

| ID | Заголовок | Severity | Связанные кейсы |
|---|---|---|---|
| BUG-UI-01 | Wiki описывает единый счётчик "Basket Items", но в UI два разных элемента с разной логикой подсчёта | Minor | UI-001 |
| BUG-UI-02 | Кнопка Reset документирована как находящаяся в верхней навигации, реально — внутри Data Viewer | Minor | UI-004 |
| BUG-UI-03 | Опечатка в сообщении о пустой корзине: "marketping" вместо "marketing" | Trivial | UI-005 |
| BUG-UI-04 | Создание товара с price = 0 молча проваливается без сообщения об ошибке | Major (UX) | UI-007 |
