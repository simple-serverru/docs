---
Автор: Команда simple-server
---

# Как установить и настроить Telegram-бота на VDS

Telegram-боты — это отличное решение для автоматизации различных задач, начиная от уведомлений и заканчивая полноценными чат-ботами для общения с пользователями. В этой статье мы рассмотрим процесс установки и настройки Telegram-бота на вашем сервере VDS с операционной системой Linux (например, Ubuntu).

## Шаг 1: Регистрация и получение токена бота

Прежде чем приступить к установке на сервер, необходимо создать самого бота в Telegram и получить токен для доступа к API.

1. Откройте Telegram и найдите бота **BotFather** (официальный бот для управления ботами).
2. Отправьте команду `/start` для начала общения с BotFather.
3. Создайте нового бота с помощью команды `/newbot`. BotFather попросит вас ввести имя бота и его уникальное имя (оно должно заканчиваться на `bot`).
4. После успешного создания бота BotFather отправит вам **токен** — строку, которая выглядит как набор символов, например `123456789:ABCDefghIJaqLmnopQRSTuvsxYZ`. **Сохраните этот токен**, так как он будет использоваться для взаимодействия с вашим ботом через API.

## Шаг 2: Подготовка сервера VDS

Теперь, когда у вас есть токен бота, можно приступать к настройке вашего сервера VDS.

Подключитесь к вашему серверу VDS по SSH. Для этого используйте команду:

```bash
ssh root@your_vds_ip
```

Убедитесь, что все необходимые пакеты обновлены:

```bash
sudo apt update && sudo apt upgrade
```

Установите Python, если он ещё не установлен (рекомендуется использовать Python 3):

```bash
sudo apt install python3 python3-pip
```

Создайте отдельную папку для вашего бота:

```bash
mkdir telegram_bot && cd telegram_bot
```

## Шаг 3: Установка библиотеки для работы с Telegram API

Для работы с Telegram API в Python существует множество библиотек, одной из самых популярных является `python-telegram-bot`. Установим её через `pip`:

```bash
pip3 install python-telegram-bot
```

## Шаг 4: Написание простого Telegram-бота

Создадим файл `bot.py` и добавим простой код для взаимодействия с ботом:

```bash
nano bot.py
```

Вставьте следующий код в файл:



```python [bot.py]
import telegram
from telegram.ext import Updater, CommandHandler

# Вставьте ваш токен бота
TOKEN = 'ваш_токен_здесь'

# Функция для обработки команды /start
def start(update, context):
    context.bot.send_message(chat_id=update.effective_chat.id, text="Привет! Я ваш бот.")

# Основная функция для запуска бота
def main():
    updater = Updater(token=TOKEN, use_context=True)
    dispatcher = updater.dispatcher

    # Добавляем обработчик для команды /start
    start_handler = CommandHandler('start', start)
    dispatcher.add_handler(start_handler)

    # Запускаем бота
    updater.start_polling()

    # Оставляем бота активным до остановки
    updater.idle()

if __name__ == '__main__':
    main()
```



Этот код создаёт простого бота, который будет отвечать на команду `/start` сообщением **"Привет! Я ваш бот."**

## Шаг 5: Запуск бота

Запустите вашего бота с помощью Python:

```bash
python3 bot.py
```

Теперь ваш бот запущен и готов к взаимодействию в Telegram. Чтобы проверить, что бот работает, откройте Telegram, найдите своего бота и отправьте команду `/start`. Если всё настроено правильно, бот ответит сообщением **"Привет! Я ваш бот."**

## Шаг 6: Автоматический запуск бота при старте системы

Чтобы бот автоматически запускался при перезагрузке сервера, можно использовать `systemd` для управления процессом.

Создайте файл `telegram_bot.service` в директории `/etc/systemd/system/`:

```bash
sudo nano /etc/systemd/system/telegram_bot.service
```

Вставьте в этот файл следующее содержимое:



```ini [/etc/systemd/system/telegram_bot.service]
[Unit]
Description=Telegram Bot
After=network.target

[Service]
ExecStart=/usr/bin/python3 /path_to_your_bot/telegram_bot/bot.py
WorkingDirectory=/path_to_your_bot/telegram_bot/
Restart=always
User=your_user

[Install]
WantedBy=multi-user.target
```



Сохраните файл и выполните следующие команды для активации сервиса:

```bash
sudo systemctl daemon-reload
sudo systemctl start telegram_bot
sudo systemctl enable telegram_bot
```

Теперь бот будет автоматически запускаться каждый раз при перезагрузке вашего сервера.

## Заключение

В этой статье мы рассмотрели, **как установить и настроить Telegram-бота на сервере VDS**. Вы создали бота, подключили его к Telegram, настроили работу через Python и обеспечили автоматический запуск бота при перезагрузке сервера.

Telegram-боты могут быть полезны для множества задач, и, используя этот базовый пример, вы можете развивать функционал вашего бота в соответствии с требованиями вашего проекта.

Если вам нужен **VDS для работы с Telegram-ботом**, вы можете заказать сервер на нашем сайте [simple-server.ru](https://simple-server.ru/).

