# Flow Kanban — лабораторная работа №16

Веб-приложение для управления проектами и задачами на Kanban-доске. Репозиторий включает React-интерфейс, REST API на Express/TypeScript, PostgreSQL/Prisma, тесты и CI workflow.

## Возможности

- регистрация, вход, JWT, роли `ADMIN`, `MANAGER`, `DEVELOPER`;
- проекты и базовая доска с колонками;
- создание/редактирование/удаление задач, приоритеты, метки, дедлайн и исполнитель;
- перетаскивание карточек между колонками;
- WIP-лимиты на стороне API;
- поиск по названию/описанию, фильтрация по приоритету, исполнителю и метке через API;
- комментарии и история изменений задач;
- валидация входных данных, bcrypt, Helmet, rate limiting;
- Docker Compose и GitHub Actions.

> Интерфейс запускается в демонстрационном режиме без сервера: можно посмотреть пример доски и создавать локальные демонстрационные карточки. Для постоянного хранения и проверки серверных функций запустите все сервисы через Docker Compose.

## Быстрый запуск через Docker

1. Установите Docker Desktop / Docker Engine с Compose.
2. В корне проекта выполните:

```bash
docker compose up --build
```

3. Откройте http://localhost:8080 — веб-интерфейс; http://localhost:4000/api/health — проверка API.
4. Зарегистрируйте пользователя через интерфейс. Первый зарегистрированный пользователь по умолчанию получает роль `DEVELOPER`; создание проектов разрешено менеджеру/администратору. Для учебной демонстрации можно назначить роль в БД:

```sql
UPDATE "User" SET role = 'MANAGER' WHERE email = 'you@example.com';
```

После изменения роли войдите заново, чтобы получить новый JWT.

## Локальный запуск без Docker

Требуется Node.js 22+ и PostgreSQL 16+.

```bash
cd backend
cp .env.example .env
npm install
npx prisma generate
npx prisma db push
npm run dev
```

В другом терминале:

```bash
cd frontend
npm install
npm run dev
```

Frontend: http://localhost:5173. В `backend/.env` задайте корректный `DATABASE_URL`, длинный `JWT_SECRET` и `CLIENT_ORIGIN=http://localhost:5173`.

## Проверки

```bash
cd backend
npm install
npx prisma generate
npm test
npm run build
```

```bash
cd frontend
npm install
npm run build
```

Swagger/OpenAPI можно добавить поверх REST API; текущие основные маршруты перечислены в `docs/API.md`. Для полноценного соответствия учебному чек-листу проведите ручные E2E-прогоны, нагрузочный тест k6 и активный скан OWASP ZAP только на локальном/тестовом окружении.

## Структура

- `frontend/` — React + TypeScript + Vite;
- `backend/src/` — Express API, middleware, auth utilities;
- `backend/prisma/schema.prisma` — схема БД;
- `backend/tests/` — unit-тесты утилит авторизации;
- `docs/` — тест-план, тест-кейсы и API;
- `.github/workflows/ci.yml` — автоматическая сборка и запуск тестов.

## Ограничения текущей учебной версии

Вложения файлов, email-уведомления, real-time WebSocket, полноценная матрица членства в проектах, refresh-токены и развёртывание в production не реализованы. CI запускает unit-тесты и сборки; интеграционные, браузерные, нагрузочные и security-тесты описаны в плане, но требуют отдельной настройки окружения и выполнения. Метрики 1000 одновременных пользователей и 99,9% доступности не заявляются без измерений.
