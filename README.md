# DevOps Pipeline

Минимальное веб-приложение на Python и Flask для демонстрации
CI/CD в GitHub Actions.

Приложение отдаёт текстовую страницу по адресу `/`.

## Технологии

- Python 3.12
- Flask
- Waitress
- Docker
- Docker Compose
- GitHub Actions
- Telegram Bot API

## Требования для запуска

- Git
- Docker Desktop с Linux-контейнерами
- Docker Compose
- PowerShell для команд ниже

## Получение проекта

```powershell
git clone [https://github.com/nightmarakesh/devops-pipeline.git](https://github.com/nightmarakesh/devops-pipeline.git)
cd devops-pipeline
```

Если проект уже скачан:

```powershell
git switch main
git pull --ff-only
```

## Запуск опубликованной версии из Docker Hub

Выполните в папке проекта:

```powershell
$env:IMAGE_REPOSITORY = "nightmarakesh/devops-pipeline"
$env:IMAGE_TAG = "v" + (Get-Content .\version -Raw).Trim()

docker compose -p devops-pipeline pull app
docker compose -p devops-pipeline up -d --no-build --pull never app
```

Откройте:

http://localhost:18080

Образ скачивается из Docker Hub. Локальная сборка не выполняется.

Список опубликованных образов:

https://hub.docker.com/r/nightmarakesh/devops-pipeline/tags



## CI/CD

Pipeline запускается после merge ветки `feature/*` или `hotfix/*`
из этого репозитория в `main`.

Этапы:

1. Параллельные проверки: ClamAV, Bandit, Ruff.
2. Два параллельных теста-заглушки.
3. Автоматическое обновление версии и коммит файла `version`.
4. Дополнение `changelog.md`, коммит и загрузка артефакта.
5. Сборка и публикация версионного Docker-образа.
6. Обновление краткого описания и README в Docker Hub.
7. Уведомление в Telegram и отправка `changelog.md`.
8. Локальный деплой через Docker Compose и проверка страницы.
9. Создание Git-тега и GitHub Release.

