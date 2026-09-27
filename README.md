# QA Cloud — Manual Testing 
Ручное тестирование приложений платформы [qacloud.dev](https://www.qacloud.dev).

## Проекты

- [`market/`](./market) — тестирование **QA Cloud Market** (e-commerce / grocery shopping): REST API (Products, Basket, Orders) + UI поверх него.
Проект по ручному тестированию учебного приложения QA Cloud Market — REST API и веб-интерфейса поверх него. Проект демонстрирует полный цикл Manual QA: анализ документации и требований (Swagger + Wiki), составление чек-листов и тест-кейсов, проведение функционального и исследовательского тестирования на трёх слоях приложения (API, данные, UI), поиск дефектов, включая расхождения между документацией и реализацией, и оформление баг-репортов с шагами воспроизведения, ожидаемым и фактическим результатом.

---

# market/

**Manual & API Testing Project | REST API Testing | UI Testing | Exploratory Testing | Bug Reporting**

Ручное тестирование API и UI приложения [QA Cloud Market](https://www.qacloud.dev/market).

## Application Under Test

**Приложение:** [qacloud.dev/market](https://www.qacloud.dev/market) — e-commerce/grocery-магазин: REST API + веб-интерфейс поверх него.

В рамках проекта протестированы:

* Products — каталог товаров (CRUD, фильтрация, сортировка)
* Basket — корзина (добавление, изменение количества, каскадное удаление)
* Orders — оформление заказов (расчёт суммы, снепшот цены, статусы)
* UI — веб-интерфейс поверх всех трёх модулей (дашборд, формы, состояния)

## Testing Types

* Functional Testing
* API Testing (REST)
* Exploratory Testing
* Boundary Value / Negative Testing
* UI Testing
* Cross-layer consistency checks (API vs UI, модуль vs модуль)

## Что сделано

- **63 тест-кейса** по 4 модулям: Products (29), Basket (10), Orders (11), UI (13)
- **Чек-лист** (`checklist.md`) со всеми проверками в компактном формате — включая те, что не выносились в отдельные тест-кейсы
- **11 найденных и задокументированных дефектов** (`bug-reports.md`), от опечаток до логических ошибок валидации
- **Postman-коллекция** (17 запросов, 3 папки: Products, Basket, Orders) — готова к импорту

Тест-кейсы частично основаны на готовом наборе из [Wiki платформы](https://www.qacloud.dev/market/wiki#test-cases) (отмечены полем `Based on` в каждом кейсе), дополнены собственными кейсами на граничные значения, типы ошибок и расхождения между документацией, API и UI.

## Найденные дефекты

| ID | Заголовок | Severity |
|---|---|---|
| BUG-P01 | `POST`/`PUT /api/groceries` принимают отрицательное значение `price` | Major |
| BUG-P02 | Несогласованная валидация `price = 0`: `POST` → 400, `PUT` → 200 | Major |
| BUG-P03 | Сообщение об ошибке при `price = 0` не соответствует причине | Minor |
| BUG-P04 | Категория `Meat` из документации отсутствует в реальных сид-данных | Major |
| BUG-B01 | `quantity = 0` в корзине возвращает "required" вместо "must be at least 1" | Minor |
| BUG-B02 | Невалидный тип `quantity`/`id` вызывает `500` с сырой ошибкой БД вместо `400` | Major |
| BUG-O01 | Невалидный формат `id` на `DELETE /api/orders/:id` вызывает `500` вместо `400` | Major |
| BUG-UI-01 | Wiki описывает несуществующий единый счётчик "Basket Items" | Minor |
| BUG-UI-02 | Кнопка Reset задокументирована не там, где реально находится | Minor |
| BUG-UI-03 | Опечатка "marketping" вместо "marketing" в пустой корзине | Trivial |
| BUG-UI-04 | Создание товара с `price = 0` — тихий отказ без сообщения об ошибке | Major (UX) |

Полные баг-репорты с шагами воспроизведения, ожидаемым/фактическим результатом — в [`bug-reports.md`](./bug-reports.md).

## Как посмотреть

1. Импортировать `QA-Cloud.postman_collection.json` в Postman.
2. Зарегистрироваться на [qacloud.dev/market/profile.html](https://www.qacloud.dev/market/profile.html), получить `api_key`.
3. Прописать ключ в переменную окружения коллекции.
4. Тест-кейсы (`test-cases-*.md`) читаются независимо от Postman — там расписаны шаги, ожидаемый/фактический результат.

## Tools and Technologies

**Testing Tools**

* Postman
* Chrome DevTools
* Wiki / Swagger документация qacloud.dev как источник требований

**Documentation**

* Markdown
* GitHub
