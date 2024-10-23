# Документация API

## Основной URL
```
https://b2b-api.ggsel.com/api/v2
```

## **POST** `/auth/token`

### Описание
Получение `access_token` и `refresh_token`. Время жизни токена
доступа - 24 часа (1 день), время жизни токена перевыпуска - 168 часов (7 дней)

### Header
| Заголовок            | Значение                   |
|----------------------|----------------------------|
| `Content-Type`        | `application/json`         |

### Параметры
| Параметр   | Тип    | Обязательный | Описание                        |
|------------|--------|--------------|---------------------------------|
| `login`    | String | Да           | Уникальное имя пользователя     |
| `password` | String | Да           | Пароль пользователя             |

### Пример
```bash
curl -X POST https://b2b-api.ggsel.com/api/v2/auth/token \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your_token>" \
-d '{
  "login": "your_login",
  "password": "0123456789abc"
}'
```

### Ответ
#### Успешный ответ `200 OK`

| Параметр       | Тип       | Описание                           |
|----------------|-----------|------------------------------------|
| `access_token` | String    | Токен доступа                      |
| `refresh_token` | String    | Токен перевыпуска основного токена |
| `token_type`   | String    | Тип токена доступа                 |


### В JSON
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer"
}
```

---

## **POST** `/auth/refresh`

### Описание
Перевыпуск `access_token` и `refresh_token`. Время жизни токена
доступа - 24 часа (1 день), время жизни токена перевыпуска - 168 часов (7 дней)

### Header
| Заголовок            | Значение                   |
|----------------------|----------------------------|
| `Content-Type`        | `application/json`         |

### Параметры
| Параметр         | Тип    | Обязательный | Описание          |
|------------------|--------|--------------|-------------------|
| `refresh_token`  | String | Да           | Токен перевыпуска |

### Пример
```bash
curl -X POST https://b2b-api.ggsel.com/api/v2/auth/refresh \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your_token>" \
-d '{
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}'
```

### Ответ
#### Успешный ответ `200 OK`

| Параметр       | Тип       | Описание                           |
|----------------|-----------|------------------------------------|
| `access_token` | String    | Токен доступа                      |
| `refresh_token` | String    | Токен перевыпуска основного токена |
| `token_type`   | String    | Тип токена доступа                 |


### В JSON
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer"
}
```

---

## **GET** `/user/me`

### Описание
Получение информации о пользователе

### Header
| Заголовок            | Значение                   |
|----------------------|----------------------------|
| `Content-Type`        | `application/json`         |
| `Authorization`       | `Bearer <your_token>`      |

### Пример
```bash
curl -X GET https://b2b-api.ggsel.com/api/v2/user/me \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your_token>" \
```

### Ответ
#### Успешный ответ `200 OK`

| Параметр           | Тип     | Описание                   |
|--------------------|---------|----------------------------|
| `login`            | String  | Логин пользователя         |
| `name`             | String  | Имя пользователя           |
| `balance`          | Decimal | Баланс пользователя        |
| `currency_version` | String  | Основная валюта продавца   |
| `role`             | String  | Роль доступа               |
| `is_active`        | Bool    | Статус доступа к продажам  |
| `is_beta`          | Bool    | Статус доступа к dev ветке |


### В JSON
```json
{
  "login": "testerSales",
  "name": "Тестер",
  "balance": 3.4303,
  "role": "Sales",
  "is_active": true,
  "is_beta": false,
  "currency_version": "USD"
}
```

---

## **GET** `/user/balance`

### Описание
Получение баланса пользователя. В отличии от `/user/me`, этот ендпоинт
возвращает только строку с балансом пользователя. Лучше всего подходит
для точечных проверок баланса, так как выполняется быстрее.

### Header
| Заголовок            | Значение                   |
|----------------------|----------------------------|
| `Content-Type`        | `application/json`         |
| `Authorization`       | `Bearer <your_token>`      |

### Пример
```bash
curl -X GET https://b2b-api.ggsel.com/api/v2/user/balance \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your_token>" \
```

### Ответ
#### Успешный ответ `200 OK`

| Параметр           | Тип     | Описание                   |
|--------------------|---------|----------------------------|
| `balance`          | Decimal | Баланс пользователя        |

### В JSON
```json
{
  "balance": 3.4303
}
```

---

## **POST** `/payment/verify`

### Описание
Запрос на выполнение проверки платежа. Транзакция добавляется
в историю, но не выполняется.

### Примечание!
Если в запросе указать валюту USD, то она автоматически
конвертируется в RUB и наоборот. В ответе на запрос,
будут указаны обе суммы. Конвертация происходит по
актуальному курсу Steam ( Не ЦБРФ ).

### Header
| Заголовок            | Значение                   |
|----------------------|----------------------------|
| `Content-Type`        | `application/json`         |
| `Authorization`       | `Bearer <your_token>`      |

### Параметры
| Параметр          | Тип     | Обязательный | Описание                                                              |
|-------------------|---------|--------------|-----------------------------------------------------------------------|
| `service_id`      | int     | Да           | Уникальный номер сервиса                                              |
| `transaction_id`  | String  | Да           | Уникальный ID транзакции, от 6 до 36 символов. Можно использовать UID |
| `account`         | String  | Да           | Логин аккаунта, на который будет совершен платеж                      |
| `amount`          | Decimal | Да           | Сумма в валюте, что будет указана ниже                                |
| `currency`        | String  | Да           | Трехсимвольный код валюты. Например: USD, RUB                         |

### Пример
```bash
curl -X POST https://b2b-api.ggsel.com/api/v2/payment/verify \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your_token>" \
-d '{
    "service_id": 92,
    "transaction_id": "1234567890-ABC",
    "account": "c_ced",
    "amount": 1.00,
    "currency": "USD"
}'
```

### Ответ
#### Успешный ответ `200 OK`

| Параметр           | Тип      | Описание                 |
|--------------------|----------|--------------------------|
| `transaction_id`   | String   | Уникальный ID транзакции |
| `amount_rub`       | Decimal  | Сумма пополнения в RUB   |
| `amount_usd`       | Decimal  | Сумма пополнения в USD   |
| `account`          | String   | Баланс пользователя      |
| `date`             | Datetime | Баланс пользователя      |
| `status`           | String   | Баланс пользователя      |
| `status_code`      | String   | Баланс пользователя      |

### В JSON
```json
{
  "transaction_id": "1234567890-ABC",
  "amount_rub": "95.81",
  "amount_usd": "1.0000",
  "account": "_dochto_",
  "date": "2024-10-14T17:08:25",
  "status": "Заявка принята и находится в процессе обработки",
  "status_code": "REQUEST_ACCEPTED"
}
```

---

## **POST** `/payment/execute`

### Описание
Запрос на выполнение платежа.

### Примечание!
Ответ на запрос, не возвращает окончательный статус о пополнении.
Успех или ошибки в пополнении возвращает callback_url.
Для точечных проверок статуса, можно использовать `/payment/status`

### Header
| Заголовок            | Значение                   |
|----------------------|----------------------------|
| `Content-Type`        | `application/json`         |
| `Authorization`       | `Bearer <your_token>`      |

### Параметры
| Параметр         | Тип     | Обязательный | Описание                                                                   |
|------------------|---------|--------------|----------------------------------------------------------------------------|
| `transaction_id` | String  | Да           | Уникальный ID транзакции, от 6 до 36 символов. Можно использовать UID      |
| `callback_url`   | String  | Нет          | URL-адрес, на который будут отправляться уведомления о статусе транзакции. |

### Пример
```bash
curl -X POST https://b2b-api.ggsel.com/api/v2/payment/execute \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your_token>" \
-d '{
    "transaction_id": "1234567890-ABC",
    "callback_url": "https://b2b-api.ggsel.com/payment/status"
}'
```

### Ответ
#### Успешный ответ `200 OK`

| Параметр           | Тип    | Описание                                                  |
|--------------------|--------|-----------------------------------------------------------|
| `success`          | Bool   | Указывает на статус успешности операции пополнения        |
| `message`          | String | Текстовое сообщение, которое описывает результат операции |
| `data`             | Dict   | Содержит детализированную информацию о пополнении         |

### В JSON
```json
{
  "success": true,
  "message": "Payment launched successfully",
  "data": {}
}
```

---

## **GET** `/payment/status`

### Описание
Запрос на проверку статуса платежа.

### Header
| Заголовок            | Значение                   |
|----------------------|----------------------------|
| `Content-Type`        | `application/json`         |
| `Authorization`       | `Bearer <your_token>`      |

### Параметры
| Параметр         | Тип     | Обязательный | Описание                                                                   |
|------------------|---------|--------------|----------------------------------------------------------------------------|
| `transaction_id` | String  | Да           | Уникальный ID транзакции, от 6 до 36 символов. Можно использовать UID      |

### Пример
```bash
curl -X GET https://b2b-api.ggsel.com/api/v2/payment/status \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your_token>" \
-d '{
    "transaction_id": "1234567890-ABC"
}'
```

### Ответ
#### Успешный ответ `200 OK`

| Параметр          | Тип      | Описание                 |
|-------------------|----------|--------------------------|
| `transaction_id`  | String   | Уникальный ID транзакции |
| `amount_rub`      | Decimal  | Сумма пополнения в RUB   |
| `amount_usd`      | Decimal  | Сумма пополнения в USD   |
| `account`         | String   | Баланс пользователя      |
| `date`            | Datetime | Баланс пользователя      |
| `status`          | String   | Баланс пользователя      |
| `status_code`     | String   | Баланс пользователя      |

### В JSON
```json
{
  "transaction_id": "1234567890-ABC",
  "amount_rub": "95.81",
  "amount_usd": "1.0000",
  "account": "_dochto_",
  "date": "2024-10-14T17:08:25",
  "status": "Баланс успешно пополнен",
  "status_code": "PAYMENT_SUCCESS"
}
```

---


## **GET** `/transaction/view`

### Описание
Запрос на получение списка транзакций.

### Header
| Заголовок            | Значение                   |
|----------------------|----------------------------|
| `Content-Type`        | `application/json`         |
| `Authorization`       | `Bearer <your_token>`      |

### Параметры
| Параметр          | Тип    | Обязательный | Описание                                                   |
|-------------------|--------|--------------|------------------------------------------------------------|
| `limit`           | Int    | Нет          | Лимит получаемых строк                                     |
| `offset`          | Int    | Нет          | Пропуск получаемых строк. Можно использовать для пагинации |
| `sort_by`         | String | Нет          | Сортировка по указанному полю                              |
| `sort_order`      | String | Нет          | Сортировка по возрастанию - asc, по убыванию - desc        |


### Пример
```bash
curl -X GET https://b2b-api.ggsel.com/api/v2/transaction/view \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your_token>" \
-d '{
    "limit": 10,
    "offset": 0,
    "sort_by": "id",
    "sort_order": "desc"
}'
```

### Ответ
#### Успешный ответ `200 OK`

| Параметр                      | Тип      | Описание                                           |
|-------------------------------|----------|----------------------------------------------------|
| `id`                          | Int      | Уникальный идентификатор транзакции внутри системы |
| `transaction_id`              | String   | Уникальный идентификатор транзакции                |
| `amount`                      | Decimal  | Сумма пополнения в RUB                             |
| `amount_usd`                  | Decimal  | Сумма пополнения в USD                             |
| `account`                     | String   | Аккаунт пополнения пользователя                    |
| `cashback`                    | Decimal  | Кэшбэк, полученный при пополнении (если применимо) |
| `date`                        | Datetime | Дата и время проведения пополнения                 |
| `issue_date`                  | Datetime | Дата и время выдачи пополнения                     |
| `status`                      | String   | Описание статуса транзакции                        |
| `status_code`                 | String   | Код статуса транзакции                             |
| `gift_code`                   | String   | Подарочный код, если применимо                     |
| `parameter.param_id`          | Integer  | Идентификатор параметра                            |
| `parameter.name`              | String   | Название параметра (например, "Пополнение")        |
| `parameter.external_param_id` | Integer  | Внешний идентификатор параметра                    |
| `store.external_id`           | Integer  | Внешний идентификатор магазина                     |
| `store.name`                  | String   | Название магазина                                  |
| `store.deduction`             | Decimal  | Комиссия магазина                                  |
| `service.service_id`          | Integer  | Идентификатор сервиса                              |
| `service.name`                | String   | Название сервиса (например, "Steam CIS")           |

### В JSON
```json
[
  {
    "id": 49593,
    "transaction_id": "1234567890-ABC",
    "account": "_dochto_",
    "amount": 95.81,
    "amount_usd": 1.0,
    "cashback": 0.02,
    "date": "2024-10-14 17:08:25",
    "issue_date": "2024-10-14 17:15:59",
    "status": "Баланс успешно пополнен",
    "status_code": "PAYMENT_SUCCESS",
    "gift_code": null,
    "parameter": {
      "param_id": 92,
      "name": "Пополнение",
      "external_param_id": 666
    },
    "store": {
      "external_id": 52,
      "name": "APIStore",
      "deduction": 0.0
    },
    "service": {
      "service_id": 92,
      "name": "Steam CIS"
    }
  }
]
```

## Допустимые статусы по транзакции

| Статус код                       | Описание                                                                 |
|----------------------------------|--------------------------------------------------------------------------|
| `DUPLICATE_TRANSACTION`          | Транзакция уже была выполнена ранее, повторное выполнение невозможно     |
| `INSUFFICIENT_FUNDS`             | Недостаточно средств на балансе для выполнения операции                  |
| `PAYMENT_VERIFICATION_FAILED`    | Ошибка при проверке платежа                                              |
| `PAYMENT_CONFIRMATION_FAILED`    | Ошибка при подтверждении платежа                                         |
| `PAYMENT_SUCCESS`                | Баланс успешно пополнен                                                  |
| `PAYMENT_IN_PROGRESS`            | Обрабатываем пополнение                                                  |
| `PURCHASE_NOT_FOUND`             | Покупка не найдена, возможно, указан неверный код или она не существует  |
| `REQUEST_ACCEPTED`               | Заявка принята и находится в процессе обработки                          |
| `CALCULATION_ERROR`              | Ошибка при выполнении расчёта                                            |
| `TOP_UP_ERROR`                   | Внутренняя ошибка при пополнении баланса                                 |

---

## Примечания

- Убедитесь, что запросы отправляются через HTTPS.
- Обрабатывайте токены безопасно и избегайте их раскрытия в клиентском коде.

Если потребуется добавить дополнительные детали или изменить информацию, дайте знать!
