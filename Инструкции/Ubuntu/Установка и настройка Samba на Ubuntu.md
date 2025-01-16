
---
Автор: Команда simple-server.ru
---

# Установка и настройка **Samba** на *Ubuntu*

## Введение

**Samba** — это программное обеспечение, позволяющее настроить общий доступ к файлам и принтерам между Linux и Windows. С помощью Samba можно организовать сетевое хранилище и предоставить доступ к файлам для пользователей Windows и других устройств в сети.

В данной статье рассмотрим процесс установки и настройки Samba на Ubuntu.

## Установка Samba

Samba доступна в стандартных репозиториях Ubuntu, поэтому её установка проста.

### 1. Обновление пакетов системы

Перед установкой обновим системные пакеты:

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Установка Samba

Устанавливаем Samba и необходимые компоненты:

```bash
sudo apt install samba -y
```

После завершения установки Samba можно проверить версию:

```bash
smbd --version
```

## Настройка Samba

### 3. Создание общей папки

Создадим папку, к которой будет предоставлен общий доступ:

```bash
sudo mkdir -p /srv/samba/share
```

Устанавливаем права доступа для всех пользователей:

```bash
sudo chmod 777 /srv/samba/share
```

### 4. Настройка конфигурационного файла

Открываем файл конфигурации Samba:

```bash
sudo nano /etc/samba/smb.conf
```

Добавляем в конец файла следующий блок:

```ini
[Public]
   path = /srv/samba/share
   browsable = yes
   writable = yes
   guest ok = yes
   create mask = 0777
   directory mask = 0777
```

Сохраняем изменения (`CTRL+X`, затем `Y` и `Enter`).

### 5. Перезапуск службы Samba

После изменений необходимо перезапустить Samba:

```bash
sudo systemctl restart smbd
sudo systemctl enable smbd
```

## Разбор конфигурации Samba

Разберём ключевые параметры, используемые в конфигурационном файле `/etc/samba/smb.conf`:

- `path = /srv/samba/share` — указывает путь к каталогу, который будет доступен через Samba.
- `browsable = yes` — делает ресурс видимым в сети.
- `writable = yes` — позволяет записывать файлы в общий каталог.
- `guest ok = yes` — разрешает доступ гостевым пользователям (без пароля).
- `create mask = 0777` — устанавливает права доступа к создаваемым файлам (чтение и запись для всех).
- `directory mask = 0777` — аналогично `create mask`, но для папок.

## Настройка пользователей Samba

Если необходимо настроить доступ по логину и паролю, добавляем пользователя:

```bash
sudo smbpasswd -a username
```

Где `username` — это имя пользователя.

Если требуется доступ только для определённой группы пользователей, можно создать новую группу и добавить её в конфигурацию:

```bash
sudo groupadd sambagroup
sudo usermod -aG sambagroup username
```

В файле `/etc/samba/smb.conf` добавляем раздел с ограничением доступа:

```ini
[SecureShare]
   path = /srv/samba/secure
   browsable = yes
   writable = yes
   valid users = @sambagroup
   force user = sambauser
   create mask = 0644
   directory mask = 0755
   comment = Защищённая папка с доступом по паролю
```

После сохранения файла перезапускаем Samba:

```bash
sudo systemctl restart smbd
```

## Открытие порта в брандмауэре

Если на сервере активен UFW, разрешаем доступ:

```bash
sudo ufw allow samba
```

## Проверка работоспособности

Проверяем статус Samba:

```bash
sudo systemctl status smbd
```

Теперь можно проверить доступ к общему ресурсу с Windows. В проводнике Windows в строке адреса вводим:

```bash
\\your_server_ip\Public
```

## Заключение

Samba — мощный инструмент для организации общего доступа к файлам между Linux и Windows. Следуя этой инструкции, вы сможете легко настроить общий ресурс и предоставить доступ к файлам в локальной сети. 🚀

