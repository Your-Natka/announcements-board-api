# Announcements Board API

Чистий JSON REST API для дошки оголошень на Express, TypeScript, Prisma, PostgreSQL, Zod та JWT access/refresh tokens.

## Рекомендована назва репозиторію

`announcements-board-api`

## Вимоги

- Node.js 20+
- PostgreSQL
- npm

## Запуск локально

1. Встановіть залежності:

   ```bash
   npm install
   ```

2. Створіть локальний env-файл і заповніть параметри:

   ```bash
   cp .env.example .env
   ```

   Переконайтеся, що PostgreSQL запущений і база `announcements_board` існує. За потреби змініть `DATABASE_URL`.

3. Створіть Prisma Client і застосуйте міграцію:

   ```bash
   npm run prisma:generate
   npm run prisma:migrate -- --name init
   ```

   У репозиторії є початкова SQL-міграція. Якщо Prisma повідомить про стан міграцій, перевірте підключення та стан вашої бази.

4. Запустіть сервер:

   ```bash
   npm run dev
   ```

   Сервер працює на `http://localhost:3000`, документація OpenAPI — `http://localhost:3000/api-docs`.

5. Перед здачею перевірте TypeScript:

   ```bash
   npm run typecheck
   npm run build
   ```

## Маршрути

### Auth

- `POST /auth/register`
- `POST /auth/login`
- `POST /auth/refresh`
- `POST /auth/logout` — потрібен `Authorization: Bearer <accessToken>`
- `GET /auth/me` — потрібен `Authorization: Bearer <accessToken>`

### Announcements

- `GET /announcements?page=1&search=laptop&sort=newest`
- `GET /announcements/:id`
- `POST /announcements` — захищений
- `PATCH /announcements/:id` — захищений, лише власник
- `DELETE /announcements/:id` — захищений, лише власник

`sort` підтримує `newest` та `oldest`. На сторінці 10 оголошень. Пошук за `title` нечутливий до регістру.

## Приклади запитів

Реєстрація:

```bash
curl -X POST http://localhost:3000/auth/register \\
  -H 'Content-Type: application/json' \\
  -d '{"username":"ivan_petrenko","email":"ivan@example.com","password":"StrongPass123","name":"Іван"}'
```

Створення оголошення (підставте access token):

```bash
curl -X POST http://localhost:3000/announcements \\
  -H 'Content-Type: application/json' \\
  -H 'Authorization: Bearer YOUR_ACCESS_TOKEN' \\
  -d '{"title":"Продам велосипед","description":"Гірський велосипед, 21 швидкість","price":8000,"category":"sale"}'
```

Оновлення токенів:

```bash
curl -X POST http://localhost:3000/auth/refresh \\
  -H 'Content-Type: application/json' \\
  -d '{"refreshToken":"YOUR_REFRESH_TOKEN"}'
```

## Безпека

- Паролі зберігаються лише як bcrypt-хеші.
- Access token діє 15 хвилин, refresh token — 7 днів.
- Refresh tokens зберігаються в БД та обертаються при refresh.
- `JWT_SECRET` і `JWT_REFRESH_SECRET` мають бути різними випадковими секретами.
- `.env` не комітиться до Git.

## Структура

```text
prisma/
  migrations/20261029000000_init/migration.sql
  schema.prisma
  client.ts
src/
  controllers/
  middleware/
  routes/
  validators/
  types/
  openapi.ts
app.ts
```
