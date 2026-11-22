# hw-3-demush2614
# ДЗ 3 — Docker

Тут простой счётчик. `GET /` показывает число, а `POST /increment` прибавляет единицу. Сейчас число хранится в Redis, у которого есть том `tetradka`.

## Как запустить

```bash
cp .env.example .env
docker compose up -d --build --wait
curl http://127.0.0.1:8080/
```

Порт задаётся через `PORTIK` в `.env`. Там же можно поменять `GREETING`. У меня стоит «Я устал, босс».

## Что проверил

**4 балла.** Собрал образ, запустил контейнер `hw3-demush` и передал приветствие через `-e GREETING`. Счётчик был 0, после `POST /increment` стал 1. [Логи](logiv.md).

**6 баллов.** Сначала запустил Compose с SQLite и отдельным томом. После `POST /increment` счётчик стал 1. Сделал `docker compose down` и `docker compose up -d`. Контейнер поменялся, а число осталось 1.

**8 баллов.** Переключил хранилище на Redis. При проверке счётчик уже был на 2. Два запроса `POST /increment` подняли его до 4. После `docker compose down` и повторного `docker compose up -d --wait` запрос `GET /` вернул те же 4.

Потом остановил только Redis и проверил ответы:

```bash
docker compose stop redis
curl -i http://127.0.0.1:8080/healthz
curl -i http://127.0.0.1:8080/readyz
curl -i -X POST http://127.0.0.1:8080/increment
```

Получилось `200`, `503`, `503`. Приложение было живо, но до счётчика добраться не могло.

После `docker compose start redis` запрос `GET /` опять вернул 4, а следующий `POST /increment` — 5. Контейнер приложения остался тем же.
