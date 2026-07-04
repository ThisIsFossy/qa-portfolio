# QA Mini Project: Saucedemo

## Объект тестирования

Сайт: https://www.saucedemo.com

Цель: протестировать базовые пользовательские сценарии интернет-магазина:
- авторизация
- работа с каталогом
- добавление и удаление товаров
- корзина
- оформление заказа

---

# 1. Чек-лист

## Login

- Вход с валидными данными
- Вход с неверным паролем
- Вход с пустым логином
- Вход с пустым паролем

## Каталог

- Отображение списка товаров
- Добавление товара в корзину
- Удаление товара из корзины
- Открытие карточки товара

## Корзина

- Отображение добавленного товара
- Удаление товара из корзины
- Переход к оформлению заказа

## Checkout

- Заполнение данных покупателя
- Проверка обязательных полей
- Переход на страницу Checkout: Overview
- Завершение заказа

---

# 2. Тест-кейсы

## TC-1: Успешный вход с валидными данными

Шаги:
1. Открыть https://www.saucedemo.com
2. Ввести логин `standard_user`
3. Ввести пароль `secret_sauce`
4. Нажать кнопку `Login`

Ожидаемый результат:  
Пользователь перенаправлен на страницу с товарами.

---

## TC-2: Вход с неверным паролем

Шаги:
1. Открыть https://www.saucedemo.com
2. Ввести логин `standard_user`
3. Ввести пароль `secret`
4. Нажать кнопку `Login`

Ожидаемый результат:  
Отображается сообщение об ошибке: `Username and password do not match`.

---

## TC-3: Добавление товара в корзину

Шаги:
1. Открыть https://www.saucedemo.com
2. Авторизоваться под `standard_user / secret_sauce`
3. Нажать кнопку `Add to cart` у любого товара

Ожидаемый результат:  
Товар добавлен в корзину, кнопка меняется на `Remove`.

---

## TC-4: Заполнение данных покупателя

Шаги:
1. Открыть https://www.saucedemo.com
2. Авторизоваться под `standard_user / secret_sauce`
3. Добавить товар в корзину
4. Перейти в корзину
5. Нажать `Checkout`
6. Ввести First Name: `Andrey`
7. Ввести Last Name: `Taran`
8. Ввести Zip Code: `300500`
9. Нажать `Continue`

Ожидаемый результат:  
Пользователь перенаправлен на страницу `Checkout: Overview` с информацией о заказе.

---

## TC-5: Проверка обязательных полей при оформлении заказа

Шаги:
1. Открыть https://www.saucedemo.com
2. Авторизоваться под `standard_user / secret_sauce`
3. Добавить товар в корзину
4. Перейти в корзину
5. Нажать `Checkout`
6. Не заполнять данные покупателя
7. Нажать `Continue`

Ожидаемый результат:  
Отображается ошибка о необходимости заполнить обязательные поля, переход дальше не происходит.

---

# 3. Баг-репорты

## BUG-1: Долгая загрузка страницы товаров при входе под performance_glitch_user

Окружение:
- Browser: Yandex Browser / Chrome
- User: `performance_glitch_user / secret_sauce`
- Page: Login / Inventory

Предусловие:
Пользователь находится на странице логина.

Шаги:
1. Открыть https://www.saucedemo.com
2. Ввести логин `performance_glitch_user`
3. Ввести пароль `secret_sauce`
4. Нажать `Login`
5. Открыть DevTools → Network
6. Проверить время загрузки запросов

Ожидаемый результат:  
Страница товаров открывается за 1–2 секунды.

Фактический результат:  
Страница товаров загружается около 5–7 секунд.

Console:
```text
Критичных ошибок, связанных с загрузкой страницы, не обнаружено.
```

Network:
```text
Самый долгий запрос: favicon.ico — около 96 ms.
Запросов длительностью 5–7 секунд не обнаружено.
```

Вывод:  
Долгая загрузка, вероятно, связана с задержкой на стороне frontend/JavaScript, а не с сетевыми запросами.

Severity: Minor  
Priority: Low

---

## BUG-2: Кнопка "Remove" не удаляет товар для пользователя error_user

Окружение:
- Browser: Yandex Browser / Chrome
- User: `error_user / secret_sauce`
- Page: Inventory

Предусловие:
Пользователь авторизован под `error_user / secret_sauce`.

Шаги:
1. Открыть https://www.saucedemo.com
2. Войти под `error_user / secret_sauce`
3. Нажать `Add to cart` у любого товара
4. Нажать `Remove` у добавленного товара
5. Открыть DevTools → Console и Network

Ожидаемый результат:  
Товар удаляется из корзины, кнопка меняется на `Add to cart`. Ошибки в Console и Network отсутствуют.

Фактический результат:  
Товар не удаляется из корзины. Кнопка `Remove` не меняется.

Console:
```text
Uncaught Error: Failed to remove item from cart.
at InventoryListItem.jsx:66:15
```

Network:
```text
Status: 503 Service Unavailable
```

Severity: Major  
Priority: Medium

---

## BUG-3: Поле "Last Name" не принимает ввод для пользователя error_user на странице Checkout

Окружение:
- Browser: Yandex Browser / Chrome
- User: `error_user / secret_sauce`
- Page: Checkout Step One

Предусловие:
Пользователь авторизован под `error_user / secret_sauce`, в корзине есть товар.

Шаги:
1. Открыть https://www.saucedemo.com
2. Войти под `error_user / secret_sauce`
3. Добавить товар в корзину
4. Перейти в корзину
5. Нажать `Checkout`
6. Ввести значение в поле `First Name`
7. Попытаться ввести значение в поле `Last Name`
8. Открыть DevTools → Console и Network

Ожидаемый результат:  
Пользователь может ввести текст в поле `Last Name`. Ошибки в Console и Network отсутствуют.

Фактический результат:  
Поле `Last Name` не принимает ввод, текст не отображается.

Console:
```text
Uncaught TypeError: Cannot read properties of undefined (reading 'value')
at onChange (CheckOutStepOne.jsx:31:47)
```

Network:
```text
POST https://submit.backtrace.io/UNIVERSE/TOKEN/json
Status: 503 Service Unavailable
```

Severity: Major  
Priority: Medium

---

# Вывод

В рамках мини-проекта были протестированы основные пользовательские сценарии сайта Saucedemo: авторизация, работа с каталогом, корзина и оформление заказа.

По итогам тестирования подготовлены:
- чек-лист
- 5 тест-кейсов
- 3 баг-репорта
