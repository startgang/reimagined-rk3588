# Правило для Cline: подключение к https://github.com/startgang/reimagined-rk3588.git

## Основные параметры репозитория

- **Remote name:** `origin`
- **Remote URL (HTTPS):** `https://github.com/startgang/reimagined-rk3588.git`
- **Основная ветка:** `main`

> ⚠️ **Предупреждение:** использовать только HTTPS-подключение. Никогда не переключаться на SSH-URL без явного указания пользователя.

## Проверка текущего состояния

Перед любой git-операцией Cline обязан выполнить:

```bash
git status
git remote -v
```

Ожидаемый вывод для remote:

```bash
origin  https://github.com/startgang/reimagined-rk3588.git (fetch)
origin  https://github.com/startgang/reimagined-rk3588.git (push)
```

## Инициализация репозитория

Если git ещё не инициализирован или remote отсутствует:

```bash
git init
git remote remove origin 2>/dev/null || true
git remote add origin https://github.com/startgang/reimagined-rk3588.git
git branch -M main
```

Если на GitHub уже есть коммиты:

```bash
git pull origin main --allow-unrelated-histories
```

## Первый push

```bash
git add .
git commit -m "chore: initial commit"
git push -u origin main
```

## Регламент работы с ветками

1. Основная ветка — `main`.
2. Ветки для задач:
   - `feature/<name>` — новые фичи
   - `fix/<name>` — исправления
3. Мерж в `main` — только через Pull Request, если не указано иное.

## Правила коммитов

- Формат сообщения: `<type>: <short description>`
- Допустимые типы:
  - `feat`
  - `fix`
  - `docs`
  - `refactor`
  - `test`
  - `chore`
- Запрещено коммитить:
  - секреты и токены
  - `.env` и аналогичные файлы
  - артефакты сборки
  - временные файлы

## Push и синхронизация

Перед каждым push:

```bash
git pull --rebase origin main
git push -u origin main
```

## Запрещённые действия без подтверждения пользователя

> ⚠️ **Предупреждение:** следующие команды выполняются только после явного согласия пользователя.

- `git push --force` / `git push -f`
- `git reset --hard` на удалённых ветках
- удаление remote `origin`
- изменение URL remote на другой репозиторий
- `git rebase` с перезаписью уже опубликованной истории

## Обработка конфликтов

> ⚠️ **Предупреждение:** при конфликтах merge/rebase запрещено автоматически выполнять `--abort`.

Порядок действий:

1. Показать пользователю список конфликтующих файлов.
2. Запросить решение по каждому конфликту.
3. Дождаться подтверждения перед продолжением операции.

## Проверка HTTPS-доступа

```bash
git ls-remote https://github.com/startgang/reimagined-rk3588.git
```

Если доступ не настроен (нет credentials/credential-helper) — сообщить пользователю и **не пытаться** переходить на SSH.

## Гигигнор

Все правила игнорирования определены в `.gitignore` в корне репозитория — использовать его как единый источник истины.

Ключевые правила:

- В репозиторий **не попадают**: IDE-конфигурация, Python-артефакты (`__pycache__/`, `.venv/`, `*.py[cod]`), артефакты сборки и прошивки (`output/`, `rockdev/`, `*.img`, `*.bin`, `*.blf`, `*.rksd`, `*.uf`), секреты (`.env*`, `*.pem`, `*.key`, `id_rsa*`), логи и временные файлы (`*.log`, `*.tmp`, `*.bak`), служебные файлы ОС (`Thumbs.db`, `.DS_Store`).
- В репозиторий **попадают**: `.clinerules/` (правила проекта), PDF-даташиты в `hardware/components/`, исходники `.drawio` (экспортированные SVG по усмотрению автора).
- Перед коммитом проверять `git status` и убедиться, что в staging-области нет игнорируемых артефактов, добавленных ранее:

```bash
git status
git ls-files --others --exclude-standard
```