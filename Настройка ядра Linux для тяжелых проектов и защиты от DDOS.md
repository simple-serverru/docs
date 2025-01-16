---
Автор: Команда simple-server
---

# Настройка ядра Linux для тяжелых проектов и защиты от DDoS

Сейчас речь пойдет о том, как можно настроить ядро Linux сервера, чтобы он смог переваривать большое количество запросов, а также защититься от DDoS-атак. Всё будет кратко с небольшими описаниями.

Много материалов я изучил, чтобы понять, какие значения оптимальны, а также перепробовал множество настроек. Некоторые решения помогли мне снизить высокую нагрузку на сервер. Грамотная настройка ядра позволяет также защититься от **SYN flood** атак.

## Фильтр обратного пути (rp_filter)

Параметр включает фильтр обратного пути, проще говоря, активируется защита от подмены адресов (спуфинга).

```bash
cat /proc/sys/net/ipv4/conf/all/rp_filter
```

По умолчанию отключен (`0`). Рекомендуется включить строгий режим проверки (`1`):

```bash
sysctl -w net.ipv4.conf.all.rp_filter=1
sysctl -w net.ipv4.conf.eth0.rp_filter=1
```

## Запрет маршрутизации от источников (accept_source_route)

Отключает маршрутизацию от источников.

```bash
cat /proc/sys/net/ipv4/conf/all/accept_source_route
```

По умолчанию отключено (`0`). Если у вас включено, отключите на всех интерфейсах:

```bash
sysctl -w net.ipv4.conf.all.accept_source_route=0
sysctl -w net.ipv4.conf.lo.accept_source_route=0
sysctl -w net.ipv4.conf.eth0.accept_source_route=0
sysctl -w net.ipv4.conf.default.accept_source_route=0
```

## Отключение ICMP-перенаправлений

Запрещает серверу принимать и отправлять ICMP пакеты перенаправления.

```bash
sysctl -w net.ipv4.conf.all.accept_redirects=0
sysctl -w net.ipv4.conf.all.secure_redirects=0
sysctl -w net.ipv4.conf.all.send_redirects=0
```

## Отключение широковещательных ICMP-запросов

```bash
sysctl -w net.ipv4.icmp_echo_ignore_broadcasts=1
sysctl -w net.ipv4.icmp_ignore_bogus_error_responses=1
```

## Защита от SYN-флуда (tcp_syncookies)

```bash
sysctl -w net.ipv4.tcp_syncookies=1
```

## Настройка максимального числа полуоткрытых соединений

```bash
sysctl -w net.ipv4.tcp_max_syn_backlog=4096
sysctl -w net.ipv4.tcp_synack_retries=1
```

## Оптимизация параметров TCP

```bash
sysctl -w net.ipv4.tcp_max_orphans=65536
sysctl -w net.ipv4.tcp_fin_timeout=10
sysctl -w net.ipv4.tcp_keepalive_time=60
sysctl -w net.ipv4.tcp_keepalive_intvl=15
sysctl -w net.ipv4.tcp_keepalive_probes=5
sysctl -w net.core.netdev_max_backlog=1000
sysctl -w net.core.somaxconn=15000
sysctl -w net.ipv4.tcp_rmem="4096 87380 16777216"
sysctl -w net.ipv4.tcp_wmem="4096 65536 16777216"
```

## Увеличение диапазона локальных портов

```bash
sysctl -w net.ipv4.ip_local_port_range="1024 65535"
```

## Улучшение управления TCP-подключениями

```bash
sysctl -w net.ipv4.tcp_tw_reuse=1
sysctl -w net.ipv4.tcp_window_scaling=1
sysctl -w net.ipv4.tcp_rfc1337=1
sysctl -w net.ipv4.tcp_congestion_control=htcp
sysctl -w net.ipv4.tcp_no_metrics_save=1
```

## Отключение переадресации пакетов (если сервер не является маршрутизатором)

```bash
sysctl -w net.ipv4.ip_forward=0
```

## Применение изменений

Чтобы сохранить настройки после перезагрузки, добавьте их в файл `/etc/sysctl.conf`:

```bash
echo "net.ipv4.conf.all.rp_filter=1" >> /etc/sysctl.conf
echo "net.ipv4.conf.all.accept_source_route=0" >> /etc/sysctl.conf
echo "net.ipv4.conf.all.accept_redirects=0" >> /etc/sysctl.conf
echo "net.ipv4.conf.all.secure_redirects=0" >> /etc/sysctl.conf
echo "net.ipv4.conf.all.send_redirects=0" >> /etc/sysctl.conf
echo "net.ipv4.icmp_echo_ignore_broadcasts=1" >> /etc/sysctl.conf
echo "net.ipv4.icmp_ignore_bogus_error_responses=1" >> /etc/sysctl.conf
echo "net.ipv4.tcp_syncookies=1" >> /etc/sysctl.conf
echo "net.ipv4.tcp_max_syn_backlog=4096" >> /etc/sysctl.conf
echo "net.ipv4.tcp_synack_retries=1" >> /etc/sysctl.conf
echo "net.ipv4.tcp_max_orphans=65536" >> /etc/sysctl.conf
echo "net.ipv4.tcp_fin_timeout=10" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_time=60" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_intvl=15" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_probes=5" >> /etc/sysctl.conf
echo "net.ipv4.ip_local_port_range=\"1024 65535\"" >> /etc/sysctl.conf
echo "net.ipv4.tcp_tw_reuse=1" >> /etc/sysctl.conf
echo "net.ipv4.tcp_window_scaling=1" >> /etc/sysctl.conf
echo "net.ipv4.tcp_rfc1337=1" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=htcp" >> /etc/sysctl.conf
echo "net.ipv4.tcp_no_metrics_save=1" >> /etc/sysctl.conf
echo "net.ipv4.ip_forward=0" >> /etc/sysctl.conf
```

Примените настройки командой:

```bash
sysctl -p
```

## Заключение

Настроив параметры ядра Linux, можно значительно повысить производительность сервера и защитить его от DDoS-атак. Эти настройки помогут вашему серверу справляться с высокой нагрузкой и повысить безопасность.

