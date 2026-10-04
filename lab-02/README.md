# Лабораторна робота №2. Віддалені репозиторії, submodule, pre-commit hook

Практика виконувалась у віддаленому репозиторії ~/Projects/remote-demo та локальних ~/Projects/remote-demo-clone, ~/Projects/remote-demo-clone2.

## Завдання 1. Робота з віддаленим репозиторієм

**Мета:** відпрацювати налаштування віддаленого репозиторію (`git remote add`), команди `push`/`pull`/`fetch` і основи синхронізації.

### Хід виконання

Створення порожнього репозиторію `remote-demo` на GitHub (без README) та локального репозиторію з першим комітом:

```
$ cd ~/Projects
$ mkdir remote-demo
$ cd remote-demo
$ git init -b main
Initialized empty Git repository in /home/valeriia/Projects/remote-demo/.git/

$ echo "# Remote demo" > README.md
$ git add README.md
$ git commit -m "Initial commit"
[main (root-commit) 43e421b] Initial commit
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```

Підключення віддаленого репозиторію як `origin`:

```
$ git remote add origin git@github.com:xaarlox/remote-demo.git
$ git remote -v
origin	git@github.com:xaarlox/remote-demo.git (fetch)
origin	git@github.com:xaarlox/remote-demo.git (push)
```

Перший `push` із прив'язкою локальної гілки до віддаленої:

```
$ git push -u origin main
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 233 bytes | 233.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To github.com:xaarlox/remote-demo.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

Клонування цього ж репозиторію в іншу папку (імітація другого користувача/комп'ютера):

```
$ cd ..
$ git clone git@github.com:xaarlox/remote-demo.git remote-demo-clone
Cloning into 'remote-demo-clone'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (3/3), done.

$ cd remote-demo-clone
$ cat README.md
# Remote demo
```

Зміна в оригінальному репозиторії та її публікація:

```
$ cd ../remote-demo
$ echo "2nd line" >> README.md
$ git commit -am "Update README"
[main b045a8d] Update README
 1 file changed, 1 insertion(+)

$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Writing objects: 100% (3/3), 277 bytes | 277.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To github.com:xaarlox/remote-demo.git
   43e421b..b045a8d  main -> main
```

Синхронізація другого клону: спочатку перевірка нових комітів без їх застосування (`fetch`), потім застосування (`pull`):

```
$ cd ../remote-demo-clone
$ git fetch origin
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (3/3), 257 bytes | 257.00 KiB/s, done.
From github.com:xaarlox/remote-demo
   43e421b..b045a8d  main       -> origin/main

$ git log main..origin/main
commit b045a8d1df990125b56a1e72840af679e1c93f5d (origin/main, origin/HEAD)
Author: Valeriia Fedorenko <xaarlox@gmail.com>
Date:   Sun Oct 4 13:57:07 2026 +0300

    Update README

$ git pull
Updating 43e421b..b045a8d
Fast-forward
 README.md | 1 +
 1 file changed, 1 insertion(+)

$ cat README.md
# Remote demo
2nd line
```

### Пояснення

- `git remote add origin <url>` прив'язує локальний репозиторій до віддаленого під іменем `origin`. `git remote -v` підтверджує, що адреса налаштована і для завантаження (`fetch`), і для вивантаження (`push`).
- `git push -u origin main` публікує гілку й одночасно запам'ятовує зв'язок (`-u`, upstream), тому надалі достатньо просто `git push` без аргументів.
- Другий клон імітує роботу іншого розробника над тим самим репозиторієм.
- `git fetch` лише завантажує нові об'єкти та оновлює вказівник `origin/main`, **не чіпаючи** робочі файли. Це дозволяє спершу переглянути зміни (`git log main..origin/main` показує коміти, які є на сервері, але яких ще немає локально).
- `git pull` виконує `fetch` + `merge` в одній команді: тут відбулося `Fast-forward`, бо локальна гілка не мала власних нових комітів, і Git просто пересунув вказівник `main` уперед.
---

## Завдання 2. Використання підмодулів (submodule)

**Мета:** ознайомитись із механізмом submodule, командами `git submodule init`/`update`, питаннями версіонування залежностей.

### Хід виконання

Додавання зовнішнього репозиторію `octocat/Hello-World` як підмодуля в `remote-demo`:

```
$ cd ~/Projects/remote-demo
$ git submodule add https://github.com/octocat/Hello-World.git libs/hello-world
Cloning into '/home/valeriia/Projects/remote-demo/libs/hello-world'...
remote: Enumerating objects: 13, done.
remote: Total 13 (delta 0), reused 0 (delta 0), pack-reused 13 (from 1)
Receiving objects: 100% (13/13), done.

$ cat .gitmodules
[submodule "libs/hello-world"]
	path = libs/hello-world
	url = https://github.com/octocat/Hello-World.git
```

Коміт і публікація змін (додано `.gitmodules` та посилання на підмодуль):

```
$ git commit -m "Add Hello-World as submodule"
[main 09d6042] Add Hello-World as submodule
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 libs/hello-world

$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 16 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 465 bytes | 465.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To github.com:xaarlox/remote-demo.git
   b045a8d..09d6042  main -> main
```

Перевірка, що звичайний `git clone` не завантажує вміст підмодуля автоматично:

```
$ cd ..
$ git clone git@github.com:xaarlox/remote-demo.git remote-demo-clone2
Cloning into 'remote-demo-clone2'...
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 10 (delta 0), reused 10 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (10/10), done.

$ cd remote-demo-clone2
$ ls libs/hello-world
```
(папка порожня)

Ініціалізація та завантаження вмісту підмодуля:

```
$ git submodule init
Submodule 'libs/hello-world' (https://github.com/octocat/Hello-World.git) registered for path 'libs/hello-world'

$ git submodule update
Cloning into '/home/valeriia/Projects/remote-demo-clone2/libs/hello-world'...
Submodule path 'libs/hello-world': checked out '7fd1a60b01f91b314f59955a4e4d4e80d8edf11d'

$ ls libs/hello-world
README
```

Альтернативний спосіб - клонування одразу з підмодулями одним прапорцем:

```
$ git clone --recurse-submodules git@github.com:xaarlox/remote-demo.git
Cloning into 'remote-demo'...
...
Submodule 'libs/hello-world' (https://github.com/octocat/Hello-World.git) registered for path 'libs/hello-world'
Cloning into '.../remote-demo/libs/hello-world'...
...
Submodule path 'libs/hello-world': checked out '7fd1a60b01f91b314f59955a4e4d4e80d8edf11d'
```

Оновлення підмодуля до іншого коміту та фіксація цього в головному репозиторії:

```
$ cd libs/hello-world
$ git log --oneline -1
7fd1a60 (HEAD, origin/master, origin/HEAD, master) Merge pull request #6 from Spaceghost/patch-1

$ git fetch
$ git checkout 553c207
Previous HEAD position was 7fd1a60 Merge pull request #6 from Spaceghost/patch-1
HEAD is now at 553c207 first commit

$ cd ../..
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   libs/hello-world (new commits)

no changes added to commit (use "git add" and/or "git commit -a")

$ git add libs/hello-world
$ git commit -m "Update hello-world submodule to latest commit"
[main 75ee643] Update hello-world submodule to latest commit
 1 file changed, 1 insertion(+), 1 deletion(-)

$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 16 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 320 bytes | 320.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:xaarlox/remote-demo.git
   09d6042..75ee643  main -> main
```

### Пояснення

- `git submodule add <url> <шлях>` додає зовнішній репозиторій як підмодуль: створює (чи доповнює) файл `.gitmodules` з конфігурацією (шлях і URL) і фіксує у головному репозиторії спеціальний запис `160000` - це не звичайний файл чи папка, а **посилання на конкретний коміт** підмодуля.
- Звичайний `git clone` **не** завантажує вміст підмодулів: папка `libs/hello-world` після клонування порожня, доки не виконати `git submodule init` (реєструє підмодуль з `.gitmodules` у локальній конфігурації) та `git submodule update` (клонує сам вміст на потрібний коміт). Прапорець `--recurse-submodules` при `git clone` робить обидва ці кроки автоматично.
- Оновлення залежності відбувається у два етапи: спочатку всередині самого підмодуля (`cd libs/hello-world`, `git checkout <інший коміт>`), а потім у головному репозиторії потрібно **окремо зафіксувати** нове посилання (`git add libs/hello-world`, `git commit`), інакше головний репозиторій далі вказуватиме на старий коміт підмодуля.
- `git status` у головному репозиторії показує зміну підмодуля як `modified: libs/hello-world (new commits)` - це сигнал, що посилання на версію залежності відрізняється від зафіксованого.
---

## Завдання 3. Автоматизація перевірок перед комітом (pre-commit hook)

**Мета:** показати можливості Git-хуків для впровадження стандартів кодування та автоматизації робочого процесу.

### Хід виконання

Встановлення лінтера `flake8` (системний пакет Fedora):

```
$ flake8 --version
6.1.0 (mccabe: 0.7.0, pycodestyle: 2.12.1, pyflakes: 3.1.0) CPython 3.14.7 on Linux
```

Створення скрипта `.git/hooks/pre-commit`:

```
$ cd ~/Projects/remote-demo
$ nano .git/hooks/pre-commit
$ cat .git/hooks/pre-commit
```

```bash
#!/usr/bin/env bash

echo "Running flake8 before commit..."

STAGED_PY_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.py$')

if [ -z "$STAGED_PY_FILES" ]; then
    echo "No Python files staged, skipping lint."
    exit 0
fi

flake8 $STAGED_PY_FILES

if [ $? -ne 0 ]; then
    echo "[!] Lint errors found. Commit aborted."
    exit 1
fi

echo "Lint passed."
exit 0
```

Надання файлу прав на виконання (без цього Git ігнорує хук):

```
$ chmod +x .git/hooks/pre-commit
```

**Перевірка 1: файл із помилками стилю - коміт має бути заблоковано.**

```
$ cat > bad.py << 'EOF'
import os
x=1
EOF

$ git add bad.py
$ git commit -m "Add bad.py"
Running flake8 before commit...
bad.py:1:1: F401 'os' imported but unused
bad.py:2:2: E225 missing whitespace around operator
[!] Lint errors found. Commit aborted.
```

Коміт не створено - `git commit` завершився з кодом помилки через `exit 1` у хуку.

**Перевірка 2: виправлений файл - коміт має пройти.**

```
$ cat > bad.py << 'EOF'
x = 1
print(x)
EOF

$ git add bad.py
$ git commit -m "Add bad.py with correct style"
Running flake8 before commit...
Lint passed.
[main 0521ad8] Add bad.py with correct style
 1 file changed, 2 insertions(+)
 create mode 100644 bad.py
```

### Пояснення

- Git-хуки - це скрипти в папці `.git/hooks/`, які Git автоматично запускає на певних етапах роботи (перед комітом, перед push тощо). Ця папка **не версіонується** і не потрапляє в `git push`/`clone`, тому хук діє лише локально, в одного розробника.
- Файл `pre-commit` запускається автоматично перед створенням коміту. Якщо скрипт завершується з кодом `0` - Git продовжує коміт; якщо з ненульовим кодом (`exit 1`) - коміт скасовується.
- Скрипт бере список файлів, доданих в індекс (`git diff --cached --name-only --diff-filter=ACM`), відбирає з них лише `.py`-файли та передає їх `flake8`. Якщо Python-файлів серед змін немає, перевірка пропускається (`exit 0`).
- `$?` у bash містить код завершення попередньої команди (`flake8`): `0` означає відсутність помилок, будь-яке інше значення - знайдені зауваження.
- У першій спробі `flake8` знайшов невикористаний імпорт (`F401`) і відсутність пробілів навколо `=` (`E225`), тому коміт було заблоковано ще до того, як запис потрапив в історію. У другій спробі, після виправлення стилю, перевірка пройшла і коміт відбувся.
---

## Висновки

У ході лабораторної роботи було відпрацьовано три механізми командної роботи з Git:

1. Налаштовано зв'язок локального репозиторію з віддаленим (`git remote add`), виконано перший `push`, а також продемонстровано різницю між `git fetch` (лише завантажує дані) та `git pull` (завантажує й одразу зливає) на прикладі другого клону репозиторію.
2. Додано зовнішню бібліотеку як Git submodule, розглянуто, що головний репозиторій зберігає лише посилання на коміт залежності, і показано повний цикл: додавання (`submodule add`), ініціалізацію в новому клоні (`submodule init` / `submodule update`) та оновлення версії підмодуля з фіксацією цього в головному репозиторії.
3. Створено pre-commit hook, який запускає лінтер `flake8` перед кожним комітом і блокує коміт, якщо знайдено помилки стилю коду, та перевірено його роботу на прикладі файлу з помилками й без них.
