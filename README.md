# YouTube Shorts Bot

Telegram бот для создания коротких видео из YouTube роликов через API opus.pro

##  Быстрый старт

### 1. Установка зависимостей

```bash
# Клонируйте репозиторий
git clone <repository-url>
cd youtube-shorts-bot

# Установите Python зависимости
pip install -r requirements.txt
```

### 2. Настройка окружения

```bash
# Скопируйте файл конфигурации
cp .env.example .env

# Отредактируйте .env файл
nano .env
```

### 3. Настройка переменных окружения

Заполните `.env` файл:

```env
# Получите токен от @BotFather в Telegram
BOT_TOKEN=1234567890:ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghi

# URL вашего сервера (для webhook в продакшене)
WEBHOOK_BASE_URL=https://yourdomain.com

# Секрет для webhook (любая случайная строка)
WEBHOOK_SECRET=your_secure_webhook_secret_here

# MongoDB подключение
MONGO_URI=mongodb://localhost:27017/youtube_shorts_bot

# API ключи opus.pro (получите на https://opus.pro)
OPUS_API_URL=https://api.opus.pro
OPUS_API_KEY=your_opus_pro_api_key_here
OPUS_WEBHOOK_SECRET=your_opus_webhook_secret_here

```

### 4. Установка MongoDB

#### Локально:
```bash
# Ubuntu/Debian
sudo apt install mongodb
sudo systemctl start mongodb

# macOS
brew install mongodb-community
brew services start mongodb-community

# Windows
# Скачайте с https://www.mongodb.com/try/download/community
```

#### Через Docker:
```bash
docker run -d -p 27017:27017 --name mongodb mongo:latest
```

##  Запуск приложения

### Режим разработки (Polling)

```bash
# Запуск бота с polling (для тестирования)
python run.py
```

### Режим продакшн (Webhook)

```bash
# 1. Запустите API сервер для webhook
python api/main.py

# 2. В другом терминале установите webhook
python -c "
import asyncio
from bot.main import set_webhook
asyncio.run(set_webhook())
"

# 3. Запустите бота
python bot/main.py
```

### Docker Compose (рекомендуется для продакшна)

```bash
# Запуск всех сервисов
docker-compose up -d

# Просмотр логов
docker-compose logs -f

# Остановка
docker-compose down
```

##  Тестирование

### Тест всех компонентов:
```bash
python test_project.py
```

### Тест webhook:
```bash
# Убедитесь что API сервер запущен
python api/main.py

# В другом терминале
python test_webhook.py
```

### Ручное тестирование бота:
1. Найдите вашего бота в Telegram
2. Отправьте `/start`
3. Следуйте инструкциям бота

##  Получение API ключей

### Telegram Bot Token:
1. Найдите @BotFather в Telegram
2. Отправьте `/newbot`
3. Следуйте инструкциям
4. Скопируйте токен в `.env`

### Opus.pro API:
1. Зарегистрируйтесь на https://opus.pro
2. Перейдите в API раздел
3. Создайте API ключ
4. Настройте webhook URL: `https://yourdomain.com/webhooks/opus`

##  Диагностика проблем

### Проверка подключения к MongoDB:
```bash
# Проверка статуса
systemctl status mongodb

# Подключение через CLI
mongo
> show dbs
> use youtube_shorts_bot
> show collections
```

### Проверка переменных окружения:
```bash
python -c "
from config.settings import *
print('BOT_TOKEN:', BOT_TOKEN[:10] + '...')
print('MONGO_URI:', MONGO_URI)
print('OPUS_API_URL:', OPUS_API_URL)
"
```

### Проверка портов:
```bash
# Проверка что порты свободны
netstat -tulpn | grep :8000
netstat -tulpn | grep :8080
netstat -tulpn | grep :27017
```

##  Структура проекта

```
youtube-shorts-bot/
├── bot/                 # Telegram бот
│   ├── handlers.py      # Обработчики команд
│   ├── keyboards.py     # Клавиатуры
│   ├── main.py         # Основной модуль бота
│   └── states.py       # FSM состояния
├── api/                # Webhook API
│   ├── main.py         # HTTP сервер
│   └── webhooks.py     # Обработка webhook
├── db/                 # База данных
│   └── models.py       # Модели данных
├── utils/              # Утилиты
│   ├── logger.py       # Логирование
│   ├── validators.py   # Валидация
│   └── watermark.py    # Обработка ВЗ
├── config/             # Конфигурация
│   └── settings.py     # Настройки
├── .env.example        # Пример конфигурации
├── requirements.txt    # Python зависимости
├── run.py             # Запуск для разработки
├── test_project.py    # Тесты
└── docker-compose.yml # Docker конфигурация


##  Функционал бота

1. Обработка YouTube ссылок - поддержка youtube.com и youtu.be
2. Водяные знаки - загрузка PNG/JPG/WebP до 5MB
3. Настройка ВЗ - 9 позиций, 4 размера, 4 уровня прозрачности
4. Интеграция с opus.pro - автоматическая нарезка видео
5. Уведомления - получение результатов через webhook


### "ModuleNotFoundError"
```bash
# Убедитесь что установлены зависимости
pip install -r requirements.txt
```

### "Connection refused" (MongoDB)
```bash
# Запустите MongoDB
sudo systemctl start mongodb
# или
docker run -d -p 27017:27017 mongo:latest
```

### "Invalid token" (Telegram)
- Проверьте BOT_TOKEN в .env файле
- Убедитесь что токен получен от @BotFather

### "Webhook failed"
- Проверьте WEBHOOK_BASE_URL (должен быть HTTPS)
- Убедитесь что сервер доступен извне
- Проверьте OPUS_WEBHOOK_SECRET
