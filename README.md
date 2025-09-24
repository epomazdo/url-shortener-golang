# URL SHORTENER
Сервис для сокращения URL-адресов с REST API, написанный на Go.
## Особенности
- Создание коротких ссылок с автоматической генерацией псевдонима или указанием своего
- Обновление и удаление существующих ссылок
- Перенаправление по коротким ссылкам
- Аутентификация Basic Auth для защищенных операций
- Поддержка PostgreSQL и SQLite для хранения данных
- Структурированное логирование с поддержкой разных окружений
- Конфигурация через YAML-файлы и переменные окружения
- Валидация входных данных
- Middleware для логирования запросов
## Требования
- Go 1.24.1 или выше
- PostgreSQL (опционально, можно использовать SQLite)
## Установка и запуск
1. Клонируйте репозиторий:
```bash
git clone https://github.com/epomazdo/url-shortener-golang
```

2. Установите зависимости:
```bash
go mod download
```

3. Настройте конфигурацию:
- Скопируйте `local.yaml` и настройте под свои нужды
- Установите переменную окружения `CONFIG_PATH`:
```bash
export CONFIG_PATH="./local.yaml"
```

4. Настройте базу данных:
- Для PostgreSQL установите `DATABASE_URL`:
```bash
export DATABASE_URL="postgresql://user:password@localhost:5432/db_name"
```
- Для SQLite укажите путь к файлу БД в `storage_path` в конфигурации

- 5. Запустите сервер:
```bash
go run main.go
```
## Конфигурация
Основные параметры конфигурации (файл `local.yaml`):
```yaml
env: "local" # Окружение: local, dev, prod
storage_path: "./storage/storage.db" # Путь к файлу SQLite (только для SQLite)
database_url: "" # URL для подключения к PostgreSQL
http_server:
  address: "localhost:8080" # Адрес сервера
  timeout: 4s # Таймаут запросов
  idle_timeout: 60s # Таймаут простоя
  user: "myuser" # Логин для Basic Auth
  password: "mypass" # Пароль для Basic Auth
```
## API Endpoints
**Создание короткой ссылки**
```
POST /url
Content-Type: application/json

{
  "url": "https://example.com",
  "alias": "example" // опционально
}
```

**Обновление ссылки**
```
PUT /url
Content-Type: application/json

{
  "alias": "example",
  "url": "https://newexample.com"
}
```

**Удаление ссылки**
```
DELETE /url/{alias}
```
### Публичные endpoints
**Перенаправление по короткой ссылке**
```
GET /{alias}
```

## Структура проекта
```text
url-shortener/
├── internal/
│   ├── config/           # Конфигурация приложения
│   ├── http-server/
│   │   ├── handlers/     # HTTP обработчики
│   │   │   ├── url/
│   │   │   │   ├── save/     # Создание ссылок
│   │   │   │   ├── update/   # Обновление ссылок
│   │   │   │   └── delete/   # Удаление ссылок
│   │   │   └── redirect/     # Перенаправление
│   │   └── middleware/   # Промежуточное ПО
│   ├── lib/
│   │   ├── api/response/ # Форматы ответов API
│   │   ├── logger/       # Утилиты логирования
│   │   └── random/       # Генерация случайных строк
│   └── storage/          # Слои хранения данных
├── main.go               # Точка входа
├── config.go             # Загрузка конфигурации
└── *.yaml                # Файлы конфигурации
```

## Логирование
Сервис поддерживает три уровня логирования в зависимости от окружения:
- **local** - Pretty-форматирование с цветами
- **dev** - JSON-формат с debug-уровнем
- **prod** - JSON-формат с info-уровнем
