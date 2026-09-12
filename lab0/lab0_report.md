University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Введение в веб технологии](https://ex-itmo-ict-faculty.github.io/introduction-in-web-tech/)  
Year: 2026/2027  
Group: U4225  
Author: Степанов Даниил Сергеевич  
Lab: Lab0  
Date of create: 12.09.2026  
Date of finished: — (заполняется после защиты)

# Лабораторная работа №0. Создание репозитория и настройка рабочего окружения

## Цель работы

Научиться создавать репозитории, настраивать рабочее окружение и применять основные операции Git и GitHub.

## Рабочее окружение

Работа выполнена на macOS в оболочке zsh. Установлен Git `2.50.1 (Apple Git-155)`. Для работы используется GitHub-аккаунт [dasestepanov](https://github.com/dasestepanov), для аутентификации Git — отдельный ключ Ed25519.

## Ход работы

### 1. Проверка Git и настройка SSH

Команда `git --version` вернула:

```text
git version 2.50.1 (Apple Git-155)
```

Первоначальная SSH-проверка обнаружила доступ другого аккаунта. Чтобы работать от имени автора, создан отдельный ключ:

```sh
ssh-keygen -t ed25519 -C 'dasestepanov DevOps labs' -f ~/.ssh/id_ed25519_github_dasestepanov
```

Публичная часть ключа добавлена в GitHub → Settings → SSH and GPG keys под названием `DevOps labs`, тип — Authentication Key. Приватная часть хранится на компьютере вне репозитория.

Проверка выполнена с явным выбором ключа и без использования настроек другого аккаунта:

```sh
ssh -F /dev/null -i ~/.ssh/id_ed25519_github_dasestepanov -o IdentitiesOnly=yes -o BatchMode=yes -o StrictHostKeyChecking=yes -T git@github.com
```

Результат:

```text
Hi dasestepanov! You've successfully authenticated, but GitHub does not provide shell access.
```

GitHub подтвердил успешную аутентификацию. Сообщение об отсутствии shell-доступа ожидаемо: GitHub принимает Git-операции, но не предоставляет интерактивную оболочку. В таком случае `ssh -T` завершается кодом 1 даже при успешной аутентификации.

### 2. Создание и клонирование репозитория

Создан публичный репозиторий [devops-lab-stepanov](https://github.com/dasestepanov/devops-lab-stepanov). При создании включено добавление README: GitHub создал начальный коммит и ветку `main`, которая служит базой для Pull Request.

Репозиторий склонирован на компьютер:

```sh
git clone https://github.com/dasestepanov/devops-lab-stepanov.git
cd devops-lab-stepanov
```

Клонирование выполнено через HTTPS. После настройки SSH адрес `origin` переключён на SSH, а выбор ключа сохранён только в конфигурации этого репозитория:

```sh
git remote set-url origin git@github.com:dasestepanov/devops-lab-stepanov.git
git config core.sshCommand 'ssh -F /dev/null -i /Users/veronika/.ssh/id_ed25519_github_dasestepanov -o IdentitiesOnly=yes'
git config user.name 'Степанов Даниил Сергеевич'
```

Email автора настроен локально командой `git config user.email` по адресу из первоначального коммита аккаунта. Настройки других проектов не изменялись. Абсолютный путь к ключу относится к компьютеру, на котором выполнена работа; на другом компьютере потребуется собственный ключ и соответствующий путь.

### 3. Подготовка файлов и ветки develop

В README добавлены описание проекта, ФИО, группа, учебный год, ссылка на GitHub, Telegram и план изучения DevOps. Файл `.gitignore` исключает служебные файлы macOS и Windows, временные файлы редакторов, локальные журналы и секреты.

Создана рабочая ветка:

```sh
git switch -c develop
```

Добавлены `CONTRIBUTING.md` с правилами участия, лицензия MIT и отчёт `lab0/lab0_report.md`. Правила участия описывают цикл отдельная ветка → изменения → проверка → коммит → Pull Request → слияние → удаление ветки.

### 4. Коммит и отправка изменений

```sh
git diff --check
git add README.md .gitignore CONTRIBUTING.md LICENSE lab0/lab0_report.md
git commit -m 'Initial project setup'
git push -u origin develop
```

Проверка форматирования изменений прошла без ошибок. Создан коммит [a925de5 — Initial project setup](https://github.com/dasestepanov/devops-lab-stepanov/commit/a925de5e37284becbaf1a58094afde9983243be8). Ветка отправлена на GitHub через SSH; `-u` настроил связь локальной ветки с `origin/develop`.

### 5. Pull Request и слияние

Создан [Pull Request №1 — Initial project setup](https://github.com/dasestepanov/devops-lab-stepanov/pull/1). Базовая ветка — `main`, ветка изменений — `develop`. В описании перечислены файлы, назначение изменений и выполненные проверки.

GitHub сообщил об отсутствии конфликтов. Pull Request слит методом merge commit: [8910604](https://github.com/dasestepanov/devops-lab-stepanov/commit/8910604). Этот способ сохранил отдельный коммит `Initial project setup` и добавил коммит слияния.

### 6. Удаление develop и обновление локальной main

```sh
git fetch origin
git switch main
git pull --ff-only
git push origin --delete develop
git branch -d develop
```

Локальная `main` обновлена до результата слияния. Удалённая и локальная ветки `develop` удалены. Использован безопасный вариант `git branch -d`, который проверяет, что изменения ветки уже слиты.

После завершения операций отчёт дополнен фактическими результатами отдельным документационным коммитом в `main`.

## Результаты и вывод

Создан GitHub-репозиторий, выполнено клонирование, настроены автор коммитов и SSH-доступ для `dasestepanov`. В проекте присутствуют README, .gitignore, CONTRIBUTING, LICENSE и отчёт. Выполнен полный цикл работы с веткой `develop`: коммит, отправка, Pull Request, слияние в `main` и удаление ветки.

Git хранит историю изменений локально; GitHub предоставляет удалённый репозиторий и интерфейс Pull Request. SSH-ключ обеспечивает аутентификацию при обмене изменениями. Удаление слитой ветки не удаляет вошедшие в `main` коммиты.

Практическая часть выполнена 12.09.2026. Защита перед преподавателем ещё не проведена, поэтому поле Date of finished не заполнено.

## Правила оформления

Использованы [предоставленные правила отчётов](https://ex-itmo-ict-faculty.github.io/introduction-in-web-tech/education/labs2025-2026/reportdesign/): Markdown, обязательная шапка, расположение `lab0/lab0_report.md`, README, .gitignore и LICENSE. Учебный год 2026/2027 указан по данным автора. Имя `devops-lab-stepanov` взято из конкретного задания №0; общие правила предлагают другое имя для общего репозитория отчётов — `2026_2027-introduction-in-web-tech-u4225-stepanov_d_s`.
