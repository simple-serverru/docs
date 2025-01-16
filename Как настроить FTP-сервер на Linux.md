---
Автор: Команда simple-server.ru
---

# Как настроить FTP-сервер на Linux

## Введение
FTP (File Transfer Protocol) — это классический протокол передачи файлов, который широко используется для загрузки и управления файлами на удаленных серверах. В этом руководстве мы рассмотрим процесс установки и настройки FTP-сервера на Linux.

## Установка FTP-сервера

Для установки FTP-сервера в зависимости от дистрибутива Linux можно использовать разные пакеты:

### Установка VSFTPD (Very Secure FTP Daemon) — рекомендуется
**Debian/Ubuntu:**
```bash
sudo apt update && sudo apt install vsftpd -y
```

**CentOS/RHEL:**
```bash
sudo yum install vsftpd -y
```

После установки сервис нужно запустить и добавить в автозагрузку:
```bash
sudo systemctl start vsftpd
sudo systemctl enable vsftpd
```

## Базовая настройка VSFTPD

Открываем конфигурационный файл:
```bash
sudo nano /etc/vsftpd.conf
```

Изменяем или добавляем следующие параметры:
```ini
anonymous_enable=NO      # Запрещаем анонимный доступ
local_enable=YES         # Разрешаем локальным пользователям вход
write_enable=YES         # Разрешаем запись
chroot_local_user=YES    # Ограничиваем доступ пользователей их домашним каталогом
```

Сохраняем и перезапускаем FTP-сервер:
```bash
sudo systemctl restart vsftpd
```

## Создание FTP-пользователя

Создадим нового пользователя для FTP-доступа:
```bash
sudo adduser ftpuser
sudo passwd ftpuser
```

Назначим правильные права доступа:
```bash
sudo chown ftpuser:ftpuser /home/ftpuser
```

## Открытие портов в брандмауэре

Разрешаем трафик для FTP в `iptables` или `firewalld`:

**Ubuntu/Debian (UFW):**
```bash
sudo ufw allow 20/tcp
sudo ufw allow 21/tcp
sudo ufw allow 990/tcp
```

**CentOS/RHEL (firewalld):**
```bash
sudo firewall-cmd --permanent --add-service=ftp
sudo firewall-cmd --reload
```

## Проверка работы FTP

Проверить доступность FTP-сервера можно с помощью команды:
```bash
ftp localhost
```

Либо с удаленного компьютера:
```bash
ftp your_server_ip
```

## Дополнительные настройки

### Ограничение пользователей в определенной директории
Создаем каталог для FTP и меняем владельца:
```bash
sudo mkdir -p /srv/ftp
sudo chown ftpuser:ftpuser /srv/ftp
```

Редактируем конфигурацию:
```bash
local_root=/srv/ftp
```

### Включение пассивного режима FTP
Добавляем в `/etc/vsftpd.conf`:
```ini
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=50000
```

Открываем указанный диапазон портов:
```bash
sudo ufw allow 40000:50000/tcp
```

## Заключение

Теперь ваш FTP-сервер готов к работе. Вы можете подключаться к нему с любого FTP-клиента, например, FileZilla. Если возникли проблемы, проверьте логи `vsftpd` с помощью:
```bash
sudo journalctl -u vsftpd --no-pager
```

Для удобного и безопасного управления файлами рекомендуем использовать **SFTP** вместо FTP, так как он работает поверх SSH и шифрует данные.

