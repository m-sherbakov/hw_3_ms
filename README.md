## РЕДМИ

Условие дз доступно по [ссылке](https://github.com/Nikfilk2030/missing_semester_hse_cs/blob/main/2026/amcs/year_1/hw_3/README.md)


## Запуск

Нужен запущенный Docker. В папке проекта:

cp .env.example .env
docker compose up -d --build

Адрес приложения: http://127.0.0.1:8080/ Порт и приветствие можно поменять в `.env`

## Проверка

Увеличить счётчик:

curl -X POST http://127.0.0.1:8080/increment

После `docker compose down` и `docker compose up -d` счётчик сохранился.

Также я останавливал Redis командой `docker compose stop redis`. В этот момент `/healthz` отвечал `200`, а `/readyz` и `/increment` отвечали `503`. После `docker compose start redis` приложение снова работало без перезапуска, счётчик сохранился.

У обоих контейнеров в `docker compose ps` был статус `healthy`.

# Результаты проверки Redis

docker compose down
docker compose up -d
curl http://127.0.0.1:8080/

Счётчик после повторного запуска: 1.

docker compose stop redis
curl -i http://127.0.0.1:8080/healthz
curl -i http://127.0.0.1:8080/readyz
curl -i -X POST http://127.0.0.1:8080/increment

Ответы: 200, 503, 503.

docker compose start redis
curl -i http://127.0.0.1:8080/readyz
curl http://127.0.0.1:8080/
curl -X POST http://127.0.0.1:8080/increment

Ответы: 200, счётчик 1, затем 2.

# Логи одного контейнера
2026-11-22 15:51:27,691 listening port=8000 storage=sqlite
2026-11-22 15:51:40,298 http 192.168.65.1 "GET / HTTP/1.1" 200 -
2026-11-22 15:51:40,314 http 192.168.65.1 "POST /increment HTTP/1.1" 200 -
2026-11-22 15:51:40,326 http 192.168.65.1 "GET / HTTP/1.1" 200 -