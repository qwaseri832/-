
# In-Memory Message Broker

<div align="center">

![Go](https://img.shields.io/badge/Go-1.26-00ADD8?style=for-the-badge&logo=go)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

### Лёгкий брокер сообщений с HTTP-интерфейсом | FIFO | Long Polling

</div>

---

## 📖 О проекте

Простой брокер сообщений на Go с HTTP-интерфейсом. Работает в памяти. Поддерживает очереди, FIFO и long polling.

---

## 📡 API

### `PUT /{queue}?v={message}`

Положить сообщение в очередь.

```bash
curl -X PUT "http://localhost:8080/pet?v=cat"
```

**Ответ:** `200 OK` или `400 Bad Request`

---

### `GET /{queue}?timeout={N}`

Забрать сообщение из очереди. Если пусто — ждёт N секунд.

```bash
curl "http://localhost:8080/pet?timeout=30"
```

**Ответ:** `200 OK` с телом сообщения или `404 Not Found`

---

## 🔄 Пример

```bash
curl -X PUT "http://localhost:8080/pet?v=cat"
curl -X PUT "http://localhost:8080/pet?v=dog"
curl "http://localhost:8080/pet"      # cat
curl "http://localhost:8080/pet"      # dog
curl "http://localhost:8080/pet"      # 404
```

---

## 🚀 Запуск

```bash
go run main.go -port 8080
```

---

## 📝 Лицензия

MIT © 2026
```
