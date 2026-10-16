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

## Проверка состояния

```powershell
docker compose -p devops-pipeline ps
docker inspect devops-pipeline --format '{{.Config.Image}}'
```

## Просмотр логов

```powershell
docker compose -p devops-pipeline logs --tail 50 app
```

## Остановка и удаление контейнера

```powershell
docker compose -p devops-pipeline down
```

## Необязательная локальная сборка

Для разработки можно собрать образ самостоятельно:

```powershell
docker build -t devops-pipeline:local .

$env:IMAGE_REPOSITORY = "devops-pipeline"
$env:IMAGE_TAG = "local"

docker compose -p devops-pipeline up -d --no-build --pull never app
```

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

Тесты-заглушки используются в соответствии с условиями задания.

## Правила версионирования

- `feature/*`: увеличение minor и сброс patch в ноль.
- `hotfix/*`: увеличение patch.

Текущая версия хранится в файле `version`.

Docker-теги и Git-теги имеют формат `vX.Y.Z`.

Новые записи в `changelog.md` добавляются выше старых.

## Метки PR

- `sec-passed`
- `test-passed`
- номер версии
- `changelog`
- `dockerhub`
- `PROD`

## Секреты GitHub Actions

Для воспроизведения pipeline необходимо настроить:

- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`
- `TELEGRAM_BOT_TOKEN`
- `TELEGRAM_CHAT_ID`

Значения секретов не хранятся в репозитории.

В текущей учебной конфигурации адрес образа для локального деплоя
задан в `.github/workflows/deploy.yml`:
`nightmarakesh/devops-pipeline`.

## Локальный runner

Для автоматического деплоя нужен зарегистрированный Windows x64 runner
с дополнительной меткой `devops-local`.

Во время деплоя должны работать:

- Docker Desktop;
- runner;
- сетевое подключение к GitHub.

В текущем окружении runner использует локальный прокси:
соответствующий VPN-клиент должен быть запущен.

Runner выполняет доверенный код на локальном компьютере.
Не следует запускать на нём непроверенные изменения.

## Особенности учебной реализации

- Merge следующих PR выполняется после завершения предыдущего pipeline.
- Полный повтор workflow повторно увеличивает версию.
- Для восстановления после ошибки сначала нужно проверить,
  какие этапы уже выполнены.
- `PROD` в этом проекте — локальный учебный стенд.