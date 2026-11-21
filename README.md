# Визаж: записи и учёт

Одностраничный сервис для визажиста: календарь записей клиентов + учёт доходов и расходов. Живёт на GitHub Pages: **https://sev174.github.io/vizazh/**

## Состав

- `index.html` — основной сервис: записи (календарь, карточка клиента), доходы/расходы, баланс, экспорт CSV.
- `alko.html` — отдельный мини-сервис «Алко-календарь» (та же база, свои таблицы).

## Режимы работы

Приложение имеет два режима, переключаются конфигом `window.APP_CONFIG` в начале файла:

1. **Демо** — поля `supabaseUrl` / `supabaseAnonKey` пустые. Данные хранятся только в localStorage браузера.
2. **Supabase (боевой)** — поля заполнены. Общая база для двоих, вход по email + паролю, живое обновление (realtime).

## Подключение Supabase (как настроено сейчас)

1. Проект Supabase: `zapisi-i-byudzhet` (регион eu-west-1).
2. Таблицы (схема `public`):
   - `appointments` — записи: `date`, `start_time`, `duration_min`, `status` (`booked`/`done`/`cancelled`), `client`, `phone`, `service`, `place`, `price`, `prepaid`, `note`.
   - `transactions` — доходы/расходы: `date`, `type` (`income`/`expense`), `category`, `amount`, `comment`, `author`.
   - `alko_people`, `alko_entries` — для «Алко-календаря».
3. RLS включён на всех таблицах: политика «signed in users full access» (роль `authenticated`, ALL). Без входа данные недоступны.
4. Realtime: все 4 таблицы в публикации `supabase_realtime`.
5. Ключ: **publishable** (`sb_publishable_...`) вписан в `APP_CONFIG` — это безопасно по задумке: ключ публичный, данные защищены входом и RLS.
6. Пользователи создаются вручную: Supabase Dashboard → Authentication → Users → Add user (email + пароль). Саморегистрация не нужна.

## Публикация

GitHub Pages раздаёт содержимое ветки `main`. Порядок обновления:

```bash
git add -A
git commit -m "описание изменений"
git push
```

Через ~1 минуту изменения видны на https://sev174.github.io/vizazh/.

## Экспорт

Кнопка «Скачать CSV» выгружает доходы/расходы (разделитель `;`, открывается в Excel).
