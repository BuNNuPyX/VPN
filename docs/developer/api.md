# API документация VPN-Service

## 1. Общая информация

API системы VPN-Service реализован в архитектурном стиле REST и предназначен для взаимодействия клиентского приложения с серверной частью системы.

### Основные принципы:
- обмен данными в формате JSON;
- использование HTTP/HTTPS;
- централизованный доступ через API Gateway;
- авторизация с использованием JWT;
- валидация входных данных.

---

## 2. Базовый URL
```
https://api.vpn-service.com
```

---

## 3. Аутентификация

Для доступа к защищенным endpoint'ам используется JWT-токен.

Токен передается в заголовке:
```
Authorization: Bearer <token>
```
---

## 4. Формат ответа

### Успешный ответ
```
{
  "status": "success",
  "data": {},
  "message": "Request successful"
}
```
### Ошибка
```
{
  "status": "error",
  "message": "Error description",
  "code": 400
}
```
---

## 5. Коды ответов

200 — OK  
201 — Created  
400 — Bad Request  
401 — Unauthorized  
403 — Forbidden  
404 — Not Found  
500 — Internal Server Error  

---

# 6. AUTH SERVICE

## 6.1 Регистрация пользователя
```
POST /auth/register
```
Описание: регистрация нового пользователя
Request:
```
{
  "email": "user@mail.com",
  "password": "12345678",
  "full_name": "Ivan Ivanov"
}
```
Response:
```
{
  "status": "success",
  "data": {
    "user_id": 1
  },
  "message": "User created"
}
```
---

## 6.2 Авторизация
```
POST /auth/login
```
Request:
```
{
  "email": "user@mail.com",
  "password": "12345678"
}
```
Response:
```
{
  "status": "success",
  "data": {
    "token": "JWT_TOKEN"
  },
  "message": "Login successful"
}
```
---

## 6.3 Выход из системы
```
POST /auth/logout
```
Описание: завершение сессии пользователя

Response:
```
{
  "status": "success",
  "message": "Logged out"
}
```
---

# 7. USER / PROFILE

## 7.1 Получение профиля
```
GET /user/profile
```
Response:
```
{
  "status": "success",
  "data": {
    "email": "user@mail.com",
    "balance": 100,
    "subscription_status": "active"
  }
}
```
---

# 8. BILLING SERVICE

## 8.1 Получение тарифов
```
GET /tariffs
```
Response:
```
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "name": "Basic",
      "price": 5,
      "duration": 30
    }
  ]
}
```
---

## 8.2 Оплата подписки
```
POST /billing/pay
```
Request:
```
{
  "tariff_id": 1,
  "payment_method": "card"
}
```
Response:
```
{
  "status": "success",
  "message": "Payment successful"
}
```
---

## 8.3 Получение подписки
```
GET /subscription
```
Response:
```
{
  "status": "success",
  "data": {
    "status": "active",
    "end_date": "2026-12-31"
  }
}
```
---

# 9. VPN SERVICE

## 9.1 Получение списка серверов
```
GET /vpn/servers
```
Response:
```
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "location": "Germany",
      "load": 40
    }
  ]
}
```
---

## 9.2 Подключение к VPN
```
POST /vpn/connect
```
Request:
```
{
  "server_id": 1
}
```
Response:
```
{
  "status": "success",
  "message": "Connected"
}
```
---

## 9.3 Отключение от VPN
```
POST /vpn/disconnect
```
Response:
```
{
  "status": "success",
  "message": "Disconnected"
}
```
---

# 10. SUPPORT SERVICE

## 10.1 Создание обращения
```
POST /support/ticket
```
Request:
```
{
  "topic": "Connection problem",
  "description": "VPN not working"
}
```
Response:
```
{
  "status": "success",
  "message": "Ticket created"
}
```
---

## 10.2 Получение списка обращений
```
GET /support/tickets
```
Response:
```
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "status": "open"
    }
  ]
}
```
---

# 11. ADMIN SERVICE

## 11.1 Получение пользователей
```
GET /admin/users
```
---

## 11.2 Назначение роли
```
PATCH /admin/users/{id}/role
```
Request:
```
{
  "role": "moderator"
}
```
---

## 11.3 Управление тарифами
```
POST /admin/tariffs  
PATCH /admin/tariffs/{id}  
DELETE /admin/tariffs/{id}  
```
---

# 12. Обработка ошибок

Примеры ошибок:
```
{
  "status": "error",
  "message": "Invalid credentials",
  "code": 401
}

{
  "status": "error",
  "message": "Subscription expired",
  "code": 403
}
```
