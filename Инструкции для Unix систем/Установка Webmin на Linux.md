---
Автор: Команда simple-server.ru
---

# Установка Webmin на Linux

## Введение

**Webmin** — это мощная веб-панель управления сервером, которая позволяет администрировать систему через удобный веб-интерфейс. С помощью Webmin можно управлять пользователями, файлами, сервисами, бэкапами, настройками сети и многими другими параметрами.

В данной статье рассмотрим процесс установки Webmin на различные дистрибутивы Linux.

## Установка Webmin вручную

Webmin можно установить на сервер с ОС:

- Ubuntu 18.04, 20.04, 22.04
- Debian 9 и выше
- CentOS 7, 8
- AlmaLinux, Rocky Linux

### 1. Подключение к серверу

Подключаемся к серверу по **SSH** под пользователем `root`:
```bash
ssh root@your_server_ip
```

### 2. Установка необходимых пакетов

Перед установкой Webmin убедитесь, что у вас обновлены системные пакеты:
```bash
sudo apt update && sudo apt upgrade -y
```

### 3. Добавление репозитория Webmin

Для установки Webmin на **Ubuntu/Debian**, добавьте официальный репозиторий:
```bash
echo "deb http://download.webmin.com/download/repository sarge contrib" | sudo tee /etc/apt/sources.list.d/webmin.list
wget -qO - http://www.webmin.com/jcameron-key.asc | sudo apt-key add -
sudo apt update
```

На **CentOS/AlmaLinux/Rocky Linux** установите Webmin из официального RPM-репозитория:
```bash
cat << EOF | sudo tee /etc/yum.repos.d/webmin.repo
[Webmin]
name=Webmin Distribution Neutral
baseurl=https://download.webmin.com/download/yum
enabled=1
gpgcheck=1
gpgkey=https://download.webmin.com/jcameron-key.asc
EOF
```

### 4. Установка Webmin

На **Ubuntu/Debian**:
```bash
sudo apt install webmin -y
```

На **CentOS/AlmaLinux/Rocky Linux**:
```bash
sudo yum install webmin -y
```

### 5. Запуск Webmin и добавление в автозагрузку

После установки запустите Webmin и добавьте его в автозагрузку:
```bash
sudo systemctl enable --now webmin
```

### 6. Доступ к Webmin

Webmin работает по **HTTPS** на порту `10000`. Чтобы получить доступ к панели, откройте браузер и перейдите по адресу:
```bash
https://your_server_ip:10000/
```

При первом входе используйте учетные данные пользователя `root` или администратора сервера.

## Настройка Webmin

### Открытие порта Webmin в брандмауэре

Если у вас включен `UFW` (Ubuntu/Debian), разрешите доступ:
```bash
sudo ufw allow 10000/tcp
```

Для `firewalld` (CentOS/RHEL/AlmaLinux):
```bash
sudo firewall-cmd --permanent --add-port=10000/tcp
sudo firewall-cmd --reload
```

### Обновление Webmin

Для обновления Webmin до последней версии используйте:
```bash
sudo apt update && sudo apt upgrade -y # Для Ubuntu/Debian
sudo yum update -y # Для CentOS/AlmaLinux
```

## Заключение

Webmin — это удобная и мощная веб-панель для администрирования серверов Linux. Следуя данной инструкции, вы сможете легко установить Webmin и управлять сервером через браузер. 🚀

