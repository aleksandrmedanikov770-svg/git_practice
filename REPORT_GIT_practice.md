# Отчет по практической работе GIT

## Тема

Работа с несколькими remote-репозиториями, синхронизация fork с upstream, создание feature-ветки и имитация Pull Request workflow.

## Цель работы

Отработать навык работы с несколькими remote:

- `origin` - fork студента Б.
- `upstream` - исходный репозиторий студента А.
- `main` - основная ветка.
- `feature/student-b` - ветка для изменений студента Б.

## Выполненные действия

1. Создан локальный bare-репозиторий `student-a-upstream.git`, имитирующий публичный GitHub-репозиторий студента А.

Команда:

```powershell
git init --bare student-a-upstream.git
```

2. Создана рабочая копия студента А `student-a-work`.

Команда:

```powershell
git clone student-a-upstream.git student-a-work
```

3. Создан bare-репозиторий `student-b-fork.git`, имитирующий fork студента Б.

Команда:

```powershell
git clone --bare student-a-upstream.git student-b-fork.git
```

4. Студент Б клонировал свой fork в папку `student-b-work`.

Команда:

```powershell
git clone student-b-fork.git student-b-work
```

5. В рабочей копии студента Б добавлен remote `upstream`.

Команда:

```powershell
git remote add upstream ../student-a-upstream.git
```

6. Студент А создал файл `README.md` в upstream-репозитории, сделал commit и отправил его в `upstream/main`.

Команды:

```powershell
git add README.md
git commit -m "docs: add upstream readme"
git branch -M main
git push -u origin main
```

7. Студент Б синхронизировал свой fork с upstream.

Команды:

```powershell
git fetch upstream
git switch -c main upstream/main
git push -u origin main
```

8. Студент Б создал ветку `feature/student-b`, добавил файл `STUDENT_B.md`, сделал commit и отправил ветку в fork.

Команды:

```powershell
git switch -c feature/student-b
git add STUDENT_B.md
git commit -m "feat: add student b contribution"
git push -u origin feature/student-b
```

9. Студент А получил feature-ветку студента Б из fork. Это имитирует просмотр Pull Request.

Команды:

```powershell
git remote add student-b-fork ../student-b-fork.git
git fetch student-b-fork feature/student-b
```

10. Студент А запросил изменения. Студент Б внес правку в ту же ветку `feature/student-b` и снова отправил ее в fork. Это имитирует автоматическое обновление Pull Request после push.

Команды:

```powershell
git add STUDENT_B.md
git commit -m "fix: address review feedback"
git push origin feature/student-b
```

11. Студент А получил обновленную feature-ветку, выполнил merge в `main` и отправил результат в upstream.

Команды:

```powershell
git fetch student-b-fork feature/student-b
git merge --no-ff student-b-fork/feature/student-b -m "merge: accept student b pull request"
git push origin main
```

12. Студент Б синхронизировал свой `main` с `upstream/main`, отправил изменения в fork и удалил локальную feature-ветку.

Команды:

```powershell
git switch main
git fetch upstream
git merge upstream/main
git push origin main
git branch -d feature/student-b
```

## Проверка результата

Итоговая история upstream:

```text
*   3934484 (HEAD -> main, origin/main) merge: accept student b pull request
|\  
| * da49628 (student-b-fork/feature/student-b) fix: address review feedback
| * df0c0ed feat: add student b contribution
|/  
* 3142cc1 docs: add upstream readme
```

Итоговая история fork:

```text
*   3934484 (HEAD -> main, upstream/main, origin/main) merge: accept student b pull request
|\  
| * da49628 (origin/feature/student-b) fix: address review feedback
| * df0c0ed feat: add student b contribution
|/  
* 3142cc1 docs: add upstream readme
```

Remote в рабочей копии студента Б:

```text
origin   student-b-fork.git
upstream student-a-upstream.git
```

Рабочая копия студента Б находится на ветке `main`, локальная ветка `feature/student-b` удалена.

## Загрузка на GitHub

По заданию требовалась загрузка на GitHub. Был указан точный репозиторий:

https://github.com/aleksandrmedanikov770-svg/git_practice

Репозиторий был проверен командой:

```powershell
git ls-remote https://github.com/aleksandrmedanikov770-svg/git_practice.git
```

Удаленный репозиторий уже содержал initial commit с файлом `README.md`. Чтобы не перезаписать удаленную историю, GitHub-репозиторий был добавлен отдельным remote `github`, после чего была выполнена синхронизация:

```powershell
git remote add github https://github.com/aleksandrmedanikov770-svg/git_practice.git
git fetch github
git merge github/main --allow-unrelated-histories -m "merge: include existing GitHub repository history"
```

При объединении возник конфликт в `README.md`, так как локальная и удаленная истории независимо добавили этот файл. Конфликт был разрешен вручную: сохранено название репозитория `git_practice` и добавлено описание выполненной практики.

После этого отчет был добавлен в репозиторий, создан финальный commit и работа была отправлена на GitHub:

```powershell
git add README.md STUDENT_B.md REPORT_GIT_practice.md REPORT_GIT_practice.docx
git commit -m "docs: add git practice report"
git push github main
```

Итоговый репозиторий: https://github.com/aleksandrmedanikov770-svg/git_practice

## Вывод

Практическое задание выполнено: создана схема `upstream` + `fork`, выполнена синхронизация через `fetch` и `merge`, создана feature-ветка, смоделирован Pull Request с запросом изменений, выполнен merge PR, после чего fork был синхронизирован с upstream. Работа и отчет загружены на GitHub в репозиторий `git_practice`.
