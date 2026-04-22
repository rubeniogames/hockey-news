# Telegram Web App: КХЛ / ВХЛ


Новостной сайт для хоккейных фанатов. Коллеги пишут в Telegram → ИИ генерирует посты → они появляются на сайте автоматически.
Мини-приложение для Telegram с двумя вкладками:


## Структура проекта
1. **Главная**
   - матчи, которые идут прямо сейчас (обновление каждые 15 секунд),
   - матчи на завтра,
   - статьи из `articles.txt`.
2. **О нас**


```
## Источники
hockey-news/
├── index.html          ← сайт (открывается в браузере)
├── posts.json          ← база постов (обновляется автоматически)
├── generate_posts.py   ← скрипт генерации через Claude API
└── .github/
    └── workflows/
        └── deploy.yml  ← автозапуск каждые 2 часа
```

## Настройка (один раз)

### 1. Telegram-бот

1. Напишите [@BotFather](https://t.me/BotFather) → `/newbot`
2. Придумайте имя и username боту
3. Скопируйте токен вида `7123456789:AAF...`
4. Добавьте бота в ваш Telegram-канал как **администратора**
5. Узнайте ID канала: перешлите любое сообщение из канала боту [@userinfobot](https://t.me/userinfobot)

### 2. Ключ Claude API

1. Зайдите на [console.anthropic.com](https://console.anthropic.com)
2. API Keys → Create Key
3. Скопируйте ключ `sk-ant-...`

### 3. Secrets в GitHub

В репозитории: **Settings → Secrets and variables → Actions → New repository secret**


| Название | Значение |
- Матчи: публичный endpoint SofaScore (КХЛ/ВХЛ фильтруются на клиенте).
|---|---|
- Статьи: файл `articles.txt` в репозитории.
| `TELEGRAM_TOKEN` | токен бота от BotFather |
| `TELEGRAM_CHAT_ID` | ID канала, например `-1001234567890` |
| `ANTHROPIC_API_KEY` | ключ `sk-ant-...` |


### 4. Включить GitHub Pages
## Формат `articles.txt`


**Settings → Pages → Source: GitHub Actions** → Save
```text

TITLE: Заголовок статьи
### 5. Первый запуск
DATE: 2026-04-22

BODY: Текст статьи (можно в несколько строк)
**Actions → Generate & Deploy → Run workflow**
---

TITLE: Еще заголовок
Через ~1 минуту сайт будет доступен по адресу:
DATE: 2026-04-23
`https://ВАШ_ЛОГИН.github.io/hockey-news/`
BODY: Еще текст

```
## Как пользоваться

- Коллеги пишут новости в Telegram-канал обычными сообщениями
- Каждые 2 часа скрипт забирает новые сообщения и генерирует посты
- Или нажмите **Actions → Run workflow** для немедленного обновления

## Формат поста в Telegram


Пишите свободным текстом, ИИ сам разберётся:
## Деплой


```
Файл `deploy.yml` разворачивает GitHub Pages:
Сегодня ЦСКА победил СКА 4:2. Шайбы забивали Панарин (2), Капризов и Гусев. 
- по расписанию 2 раза в сутки,
Матч прошёл в Москве, трибуны были заполнены до отказа.
- и вручную через `workflow_dispatch`.
```
articles.txt
articles.txt
Новый
+3
-0

TITLE: Как будет работать лента КХЛ/ВХЛ
DATE: 2026-04-22
BODY: Это тестовая статья. Добавляйте новые материалы прямо в этот файл в формате TITLE/DATE/BODY.
deploy.yml
deploy.yml
+8
-29

name: Deploy Telegram Web App

on:
  schedule:
    # 2 раза в сутки: 00:00 и 12:00 UTC
    - cron: '0 0,12 * * *'
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pages: write
      id-token: write

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Upload GitHub Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'

      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
