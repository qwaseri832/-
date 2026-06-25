Скопируйте этот код в файл `README.md` вашего репозитория message-broker:

```markdown
# In-Memory Message Broker

<div align="center">

![Go](https://img.shields.io/badge/Go-1.26-00ADD8?style=for-the-badge&logo=go)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)
![HTTP](https://img.shields.io/badge/HTTP-API-green?style=for-the-badge)

### ⚡ Лёгкий брокер сообщений с HTTP-интерфейсом | FIFO | Long Polling

</div>

---

## 📖 О проекте

**In-Memory Message Broker** — простой брокер сообщений на Go, работающий по протоколу HTTP. Это легковесная альтернатива RabbitMQ или Redis для случаев, когда не нужна персистентность и репликация.

Сервис поддерживает:
- Отправку сообщений в очереди (`PUT`)
- Получение сообщений с ожиданием (`GET` с `timeout`)
- FIFO (первым пришёл — первым ушёл)
- Long polling для блокирующего чтения

---

## 🏗 Архитектура

```
┌────────────┐     PUT /queue?v=msg     ┌─────────────────────┐
│ Отправитель │─────────────────────────▶│   In-Memory Broker  │
└────────────┘                          │                     │
                                        │  ┌────────────────┐ │
┌────────────┐     GET /queue?timeout   │  │   queue        │ │
│ Получатель  │◀─────────────────────────│  │  msgs: [msg1]  │ │
└────────────┘                          │  │  waiters: []   │ │
                                        │  └────────────────┘ │
                                        └─────────────────────┘
```

---

## 📡 API

### `PUT /{queue}?v={message}`

Отправить сообщение в очередь.

**Пример:**

```bash
curl -X PUT "http://localhost:8080/pet?v=cat"
curl -X PUT "http://localhost:8080/pet?v=dog"
```

**Ответ:**

- `200 OK` — сообщение отправлено
- `400 Bad Request` — параметр `v` отсутствует

---

### `GET /{queue}?timeout={N}`

Забрать сообщение из очереди (FIFO). Если очередь пуста — ждёт до `N` секунд.

**Пример:**

```bash
curl "http://localhost:8080/pet?timeout=30"
```

**Ответ:**

- `200 OK` — тело ответа содержит сообщение
- `404 Not Found` — сообщения нет или таймаут истёк

---

## 🔄 Пример работы

```bash
# 1. Отправить сообщения
curl -X PUT "http://localhost:8080/pet?v=cat"
curl -X PUT "http://localhost:8080/pet?v=dog"

# 2. Забрать сообщения (FIFO)
curl "http://localhost:8080/pet"      # cat
curl "http://localhost:8080/pet"      # dog

# 3. Очередь пуста
curl "http://localhost:8080/pet"      # 404 Not Found
```

---

## 🚀 Быстрый старт

### Запуск

```bash
# Запустить на порту 8080
go run main.go

# Или с указанием порта
go run main.go -port 9090
```

### Остановка

Нажмите `Ctrl+C` в терминале.

---

## 🔧 Конфигурация

| Параметр | По умолчанию | Описание |
|----------|--------------|----------|
| `-port` | `8080` | Порт для HTTP-сервера |

---

## 🧩 Как это работает

### Структуры

```go
type queue struct {
    msgs    []string      // буфер сообщений
    waiters []chan string // ожидающие получатели (FIFO)
}

type broker struct {
    mu     sync.Mutex
    queues map[string]*queue
}
```

### Логика работы

1. **`PUT`** — кладёт сообщение:
   - Если есть ожидающий получатель — отдаёт ему сразу
   - Если нет — сохраняет в буфер

2. **`GET`** — забирает сообщение:
   - Если есть в буфере — отдаёт первое
   - Если пусто и `timeout > 0` — встаёт в очередь ожидания
   - Если `timeout = 0` или истёк — возвращает `404`

---

## 🗂 Структура проекта

```
message-broker/
├── main.go          # Основной код (100 строк)
└── README.md
```

---

## 📦 Зависимости

**Нет внешних зависимостей** — только стандартная библиотека Go.

---

## 📝 Лицензия

MIT © 2026
```

---

Просто скопируйте и вставьте в `README.md`. Картинки-бейджи загружаются автоматически с [shields.io](https://shields.io/) — интернет нужен только для их отображения.
