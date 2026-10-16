# DevOps Pipeline

Минимальное веб-приложение на Python и Flask для демонстрации
CI/CD в GitHub Actions.

Главная страница возвращает текст: Hello, DevOps!

## Технологии

- Python 3.12
- Flask
- Waitress
- Docker
- Docker Compose

## Локальная сборка

Выполните в папке проекта:

```bash
docker build -t devops-pipeline:local .
```

## Локальный запуск

```bash
docker compose up -d --pull never
```

Откройте в браузере:

http://localhost:18080

## Состояние контейнера

```bash
docker compose ps
```

## Логи приложения

```bash
docker compose logs --tail 50 app
```

## Остановка и удаление контейнера

```bash
docker compose down
```

## Версия и история изменений

Текущая версия хранится в файле `version`.

История изменений хранится в файле `changelog.md`.