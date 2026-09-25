# Checklist — QA Cloud Market API

| Поле | Значение |
|---|---|
| Project | QA Cloud Market |
| Date | 2026-09-25 |
| Environment | qacloud.dev, Postman 11.x |

---

## Basket

| Module | Submodule | Summary | Status | Expected Result | Defect | Notes |
|---|---|---|---|---|---|---|
| Basket | Add item | Валидный product_id, quantity в пределах stock | Passed | 201, item создан, quantity = запрошенному | — | BASK-001 |
| Basket | Add item | Невалидный/несуществующий product_id | Passed | 404 "Product not found" | — | |
| Basket | Add item | Повторное добавление того же товара | Passed | 200, quantity объединяется в одной записи | — | BASK-002 |
| Basket | Add item | quantity > stock (за один запрос) | Passed | 400 "Not enough stock. Available: N" | — | |
| Basket | Add item | quantity > stock с учётом уже добавленного | Passed | 400, сообщение включает "Already in basket: N" | — | BASK-003 |
| Basket | Add item | quantity = 0 | **Failed** | 400 "quantity must be at least 1" | BUG-B01 | Факт: "product_id and quantity are required" |
| Basket | Add item | quantity = -1 / -2 (отрицательное) | Passed | 400 "quantity must be at least 1" | — | В отличие от quantity=0 — сообщение верное |
| Basket | Add item | quantity нечисловое, в кавычках ("asf") | **Failed** | 400 с понятным сообщением | BUG-B02 | Факт: 500, сырая ошибка БД |
| Basket | Add item | quantity спецсимволы без кавычек | **Failed** | 400, тело JSON | BUG-B02 | Факт: 400, но тело — HTML-страница |
| Basket | Add item | Без поля quantity | **Failed** | 400, тело JSON | BUG-B02 | Факт: 400, тело — HTML |
| Basket | Add item | Без тела запроса | Passed | 400 "product_id and quantity are required" | — | |
| Basket | Add item | Без поля product_id | Passed | 400 "product_id and quantity are required" | — | |
| Basket | Update quantity | Валидное новое значение | Passed | 200, quantity обновлён | — | BASK-007 |
| Basket | Update quantity | Невалидный/несуществующий id позиции | Passed | 404 | — | Сообщение "Product not found" — неточная формулировка (id относится к позиции корзины, не к товару) |
| Basket | Update quantity | quantity нечисловое ("asd") | **Failed** | 400 | BUG-B02 | Факт: 500, сырая ошибка БД |
| Basket | Update quantity | quantity спецсимволы (%12%42 и т.п.) без кавычек | **Failed** | 400, тело JSON | BUG-B02 | Факт: 400, HTML |
| Basket | Update quantity | quantity > stock | Passed | 400 "Not enough stock. Available: N" | — | |
| Basket | Update quantity | quantity = 0 | **Failed** | 400 "quantity must be at least 1" | BUG-B01 | Факт: "product_id and quantity are required" — тот же баг, что и при добавлении |
| Basket | Update quantity | quantity = -1 | Passed | 400 "quantity must be at least 1" | — | |
| Basket | Delete item | Валидный id | Passed | 200 "Item removed from basket" | — | BASK-009 |
| Basket | Delete item | Повторное удаление того же id | Passed | 404 "Basket item not found" | — | |
| Basket | Delete item | Невалидный (не-UUID) id | **Failed** | 400 | BUG-B02 | Факт: 500, сырая ошибка БД (та же природа, что и quantity) |
| Basket | Cascade | Удаление товара из каталога → исчезновение из корзины | Passed | Корзина обновляется, товар исчезает | — | BASK-006 |
| Basket | Endpoints | Очистка всей корзины (`DELETE /api/basket/clear`) | Passed | 200, корзина пуста | — | Путь отличается от документированного |

---
