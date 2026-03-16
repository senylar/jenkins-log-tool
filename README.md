# Jenkins Logs Parser

Утилита командной строки для загрузки и просмотра логов сборок Jenkins.

## Установка

# Пример для Mac OS и Ubuntu
```bash
# 1. Перемещаем файл в директорию, доступную из PATH (требуются права администратора)
sudo mv ~/Downloads/jlt /usr/local/bin/

# 2. Делаем файл исполняемым
sudo chmod +x /usr/local/bin/jlt

# 3. Проверяем
jlt --version
```

# Пример для Windows
```
# 1. Создаем папку для утилит (если её нет)
New-Item -ItemType Directory -Force -Path "C:\Tools"

# 2. Перемещаем jlt.exe из Загрузок в эту папку
Move-Item -Path "$HOME\Downloads\jlt.exe" -Destination "C:\Tools\"

# 3. Добавляем папку C:\Tools в переменную PATH (для текущего пользователя)
# Внимание: Эта команда добавит путь навсегда, перезагрузка консоли потребуется для вступления в силу.
[System.Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\Tools", [System.EnvironmentVariableTarget]::User)

# 4. Перезапустите терминал и проверьте
jlt --version
```


После установки доступна команда `jlt`.

## Быстрый старт

1. Настройте подключение к Jenkins:

```bash
jlt --setup
```

2. Загрузите логи нужной джобы:

```bash
jlt link/to/job
```

## Использование

```
jlt [--setup | --show-config] [job_url] [-b BUILDS] [-l]
```

### Аргументы

| Аргумент | Описание |
|---|---|
| `job_url` | Ссылка на джобу в Jenkins. Поддерживает вложенные папки через. |
| `-b`, `--builds` | Номера билдов для загрузки (см. форматы ниже). По умолчанию: `latest`. |
| `-l`, `--lnav` | Открыть логи в `lnav` (Linux/macOS) или Блокноте (Windows) вместо сохранения в файл. |
| `--setup` | Интерактивная настройка конфигурации (URL, пользователь, токен, путь к логам). |
| `--show-config` | Показать текущую конфигурацию. |

### Форматы выбора билдов (`-b`)

| Формат | Описание | Пример |
|---|---|---|
| `latest` | Последний билд (по умолчанию) | `jlt job` |
| `N` | Конкретный билд по номеру | `jlt job -b 42` |
| `-N` | N-й билд с конца (отрицательный индекс) | `jlt job -b -2` |
| `:-N` | Последние N билдов | `jlt job -b :-20` |
| `A:B` | Диапазон билдов от A до B включительно | `jlt job -b 30:40` |
| `A,B,C` | Перечисление нескольких номеров | `jlt job -b 1,5,10` |
| `A,B:C` | Комбинация | `jlt job -b 1,30:40` |

### Примеры

```bash
# Последний билд
jlt folder/my-job

# Конкретный билд
jlt folder/my-job -b 55

# Второй билд с конца
jlt folder/my-job -b -2

# Последние 20 билдов, открыть в lnav
jlt folder/my-job -b :-20 -l

# Диапазон билдов 30–40
jlt folder/my-job -b 30:40

# Несколько отдельных билдов
jlt folder/my-job -b 10,15,20
```

## Конфигурация

Файл конфигурации хранится в стандартном пользовательском каталоге:

- **Linux/macOS**: `~/.config/jenkins-logs/config.ini`
- **Windows**: `%APPDATA%\jenkins-log-parser\jenkins-logs\config.ini`

Пример содержимого:

```ini
[jenkins]
url = https://jenkins.example.com
username = myuser
token = myapitoken

[logs]
path = ~/logs/jenkins/

[proxy]
url = http://proxy.example.com:3128
```

| Параметр | Описание |
|---|---|
| `jenkins.url` | URL Jenkins-сервера |
| `jenkins.username` | Имя пользователя Jenkins |
| `jenkins.token` | API-токен Jenkins |
| `logs.path` | Каталог для сохранения файлов логов |
| `proxy.url` | URL HTTP-прокси (оставьте пустым, если прокси не нужен) |

## Зависимости

- [requests](https://docs.python-requests.org/) — HTTP-запросы к Jenkins API
- [platformdirs](https://github.com/platformdirs/platformdirs) — стандартные пути к каталогам
- [tqdm](https://github.com/tqdm/tqdm) — прогресс-бар при загрузке билдов

## Лицензия

MIT
