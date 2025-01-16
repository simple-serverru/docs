---
Автор: Команда simple-server.ru
---

# Как добавить скрипт в автозагрузку Ubuntu

## Введение

Добавление скрипта в автозагрузку Ubuntu может быть полезным для автоматического выполнения задач при старте системы. В этой статье рассмотрим несколько способов настройки автозапуска скриптов.

## Способ 1: Использование crontab

Редактируем cron:
```bash
crontab -e
```

Добавляем строку:
```bash
@reboot /path/to/your_script.sh
```

Сохраняем изменения и перезапускаем сервер для проверки.

## Способ 2: Добавление в rc.local

Редактируем файл `/etc/rc.local`:
```bash
sudo nano /etc/rc.local
```

Добавляем перед `exit 0`:
```bash
/path/to/your_script.sh &
```

Делаем файл исполняемым:
```bash
sudo chmod +x /etc/rc.local
```

## Способ 3: Использование systemd

Создаем новый сервис:
```bash
sudo nano /etc/systemd/system/myscript.service
```

Добавляем содержимое:
```ini
[Unit]
Description=My Startup Script
After=network.target

[Service]
ExecStart=/path/to/your_script.sh
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

Сохраняем файл и активируем сервис:
```bash
sudo systemctl enable myscript.service
sudo systemctl start myscript.service
```

## Заключение

Теперь ваш скрипт будет запускаться автоматически при старте системы. В зависимости от сценария, можно выбрать наиболее подходящий способ автозагрузки.

