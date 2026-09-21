# Лабораторна робота №1. Робота з Git

Навчальні експерименти проводяться в окремому локальному репозиторії git-sandbox, щоб не створювати вкладені репозиторії.

```
cd ~/Projects
git clone git@github.com:xaarlox/git-labs.git
mkdir -p lab-01/img
mkdir git-sandbox
```

Перевірка та налаштування Git (ім'я, пошта, назва початкової гілки):

```
git config --global user.name
git config --global user.email
git config --global init.defaultBranch
```

Результати:
+ Valeriia Fedorenko
+ xaarlox@gmail.com
+ main
---

## Завдання 1. Ініціалізація репозиторію та перший коміт

**Мета:** засвоїти команди git init, git add, git commit.

### Хід виконання

Ініціалізація репозиторію з гілкою main:

```
$ cd ~/Projects/git-sandbox
$ git init -b main
Initialized empty Git repository in /home/valeriia/Projects/git-sandbox/.git/
 
$ git status
On branch main
 
No commits yet
 
nothing to commit (create/copy files and use "git add" to track)
```

Створення файлу README.md з описом проєкту:

```
$ cat > README.md << 'EOF'
# Git Sandbox
 
A training project for a Git lab assignment. This repository is used to practice commits, branches, merges, rebasing, and tags.
 
EOF
```

Стан файлу до індексації (untracked):

```
$ git status
On branch main
 
No commits yet
 
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	README.md
 
nothing added to commit but untracked files present (use "git add" to track)
```

Додавання в індекс (staged):

```
$ git add README.md
$ git status
On branch main
 
No commits yet
 
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   README.md
```

Створення першого коміту та перегляд історії:

```
$ git commit -m "Initial commit: add README"
[main (root-commit) 89b38c5] Initial commit: add README
 1 file changed, 4 insertions(+)
 create mode 100644 README.md
 
$ git log
commit 89b38c51a6f19879a795aa0f9485000a5cce1b81 (HEAD -> main)
Author: Valeriia Fedorenko <xaarlox@gmail.com>
Date:   Mon Sep 21 13:00:41 2026 +0300
 
    Initial commit: add README
 
$ git log --oneline
89b38c5 (HEAD -> main) Initial commit: add README
```

### Пояснення

- `git init -b main` створює порожній репозиторій (папку `.git`) і задає назву початкової гілки `main`.
- `git status` показує стан файлів: спочатку `README.md` не відстежується (`Untracked`), після `git add` потрапляє в індекс (`Changes to be committed`), після `git commit` стає частиною історії.
- `git add` переносить зміни в індекс (область підготовки до коміту), а `git commit` фіксує вміст індексу як окремий знімок проєкту з унікальним хешем (`89b38c5`).
- Позначка `root-commit` означає, що це перший коміт у репозиторії, у нього немає батьківського коміту.
---


## Завдання 2. Історія змін і формат повідомлень комітів

**Мета:** навчитись формулювати інформативні повідомлення комітів і переглядати історію через git log.

### Хід виконання

Кожна зміна зафіксована окремим комітом. Повідомлення складається із заголовка (перший `-m`) та короткого опису (другий `-m`).

**Коміт 2.1. Розділ "Installation"**

```
$ cat >> README.md << 'EOF'
 
## Installation
 
Clone the repository and navigate into the project directory.
EOF
$ git add README.md
$ git commit -m "Add installation section" -m "Describe how to get the project locally."
[main 3e5962c] Add installation section
 1 file changed, 4 insertions(+)
```

**Коміт 2.2. Розділ "Usage"**

```
$ cat >> README.md << 'EOF'
 
## Usage
 
Run the program using the command `python3 app.py`
EOF
$ git commit -am "Add usage section" -m "Explain how to run the application."
[main 6abc952] Add usage section
 1 file changed, 4 insertions(+)
```

**Коміт 2.3. Редагування тексту**

```
$ sed -i 's/A training project/A training demo project/' README.md
$ git commit -am "Improve project description" -m "Make the intro sentence more specific."
[main a5fc620] Improve project description
 1 file changed, 1 insertion(+), 1 deletion(-)
```

**Коміт 2.4. Додавання файлу `app.py`**

```
$ echo 'print("Hello, Git!")' > app.py
$ git add app.py
$ git commit -m "Add app.py" -m "Simple script used in the next tasks."
[main 263d32f] Add app.py
 1 file changed, 1 insertion(+)
 create mode 100644 app.py
```

**Перегляд історії**

Коротка форма `git log --oneline`:

```
$ git log --oneline
263d32f (HEAD -> main) Add app.py
a5fc620 Improve project description
6abc952 Add usage section
3e5962c Add installation section
89b38c5 Initial commit: add README
```

Повна форма `git log` (показано два останні коміти):

```
$ git log
commit 263d32f2331716b3eda740d570e88be0d0d0c5ba (HEAD -> main)
Author: Valeriia Fedorenko <xaarlox@gmail.com>
Date:   Mon Sep 21 13:15:24 2026 +0300
 
    Add app.py
 
    Simple script used in the next tasks.
 
commit a5fc620447065624139834b63894b07ff4152a1e
Author: Valeriia Fedorenko <xaarlox@gmail.com>
Date:   Mon Sep 21 13:13:38 2026 +0300
 
    Improve project description
 
    Make the intro sentence more specific.
...
```

Статистика змін `git log --stat` (вивід скорочено, показано два останні коміти):

```
$ git log --stat
commit 263d32f2331716b3eda740d570e88be0d0d0c5ba (HEAD -> main)
Author: Valeriia Fedorenko <xaarlox@gmail.com>
Date:   Mon Sep 21 13:15:24 2026 +0300
 
    Add app.py
 
    Simple script used in the next tasks.
 
 app.py | 1 +
 1 file changed, 1 insertion(+)
 
commit a5fc620447065624139834b63894b07ff4152a1e
Author: Valeriia Fedorenko <xaarlox@gmail.com>
Date:   Mon Sep 21 13:13:38 2026 +0300
 
    Improve project description
 
    Make the intro sentence more specific.
 
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
...
```

Графічне подання історії:

```
$ git log --oneline --graph --decorate
* 263d32f (HEAD -> main) Add app.py
* a5fc620 Improve project description
* 6abc952 Add usage section
* 3e5962c Add installation section
* 89b38c5 Initial commit: add README
```

### Пояснення
 
- Один коміт відповідає одній логічній зміні, тому історію легко читати та за потреби скасовувати окремі кроки.
- Формат повідомлення: короткий заголовок у наказовому способі (`Add ...`, `Improve ...`), порожній рядок і при потребі окремий опис. Два прапорці `-m` створюють саме таку структуру.
- `git log` показує повну інформацію (хеш, автор, дата, повідомлення), `--oneline` дає компактний список, `--stat` додає перелік змінених файлів і кількість рядків, `--graph --decorate` малює структуру гілок і позначає `HEAD` та назви гілок.
- Прапорець `-a` у `git commit -am` автоматично додає в індекс зміни вже відстежуваних файлів. Нові файли треба додавати через `git add`.
---


## Завдання 3. Створення гілок і перемикання між ними

**Мета:** відпрацювати команди git branch, git switch і git diff для порівняння гілок.

### Хід виконання

Створення гілки `feature/login`, зміни в коді та коміт:

```
$ git switch -c feature/login
Switched to a new branch 'feature/login'
 
$ sed -i '1s/.*/print("Hello from login page!")/' app.py
$ echo 'def login(user): return f"Logged in: {user}"' > login.py
$ git add app.py login.py
$ git commit -m "Add login feature" -m "Change greeting and add login helper."
[feature/login 7be8adb] Add login feature
 2 files changed, 2 insertions(+), 1 deletion(-)
 create mode 100644 login.py
```

Повернення на `main` і перегляд гілок:

```
$ git switch main
Switched to branch 'main'
 
$ git branch -vv
  feature/login 7be8adb Add login feature
* main          263d32f Add app.py
```

Порівняння гілок:

```
$ git diff main..feature/login
diff --git a/app.py b/app.py
index 5d97bdd..f4e6578 100644
--- a/app.py
+++ b/app.py
@@ -1 +1 @@
-print("Hello, Git!")
+print("Hello from login page!")
diff --git a/login.py b/login.py
new file mode 100644
index 0000000..f82a819
--- /dev/null
+++ b/login.py
@@ -0,0 +1 @@
+def login(user): return f"Logged in: {user}"
```

Граф історії та перевірка стану `main`:

```
$ git log --oneline --graph --all
* 7be8adb (feature/login) Add login feature
* 263d32f (HEAD -> main) Add app.py
* a5fc620 Improve project description
* 6abc952 Add usage section
* 3e5962c Add installation section
* 89b38c5 Initial commit: add README
 
$ cat app.py
print("Hello, Git!")
 
$ ls
app.py  README.md
```

### Пояснення

- `git switch -c <назва>` створює нову гілку та одразу перемикається на неї. Гілка є лише вказівником на коміт, тому створюється миттєво.
- `git branch -vv` показує всі локальні гілки, останній коміт кожної та позначає поточну гілку зірочкою.
- `git diff main..feature/login` показує різницю між кінцевими станами двох гілок: у `feature/login` змінено перший рядок `app.py` (у `diff` рядки з `-` належать `main`, з `+` належать `feature/login`) і додано новий файл `login.py`.
- Після перемикання на `main` файл `login.py` зник, а `app.py` має початковий вміст: Git підміняє файли в робочій директорії відповідно до вибраної гілки. Це підтверджує, що зміни ізольовані у своїй гілці.
---


## Завдання 4. Злиття гілок і вирішення конфліктів

**Мета:** показати природу конфліктів, познайомитись зі статусом (git status), позначенням конфліктних ділянок і командою git merge --continue.

### Хід виконання

Щоб змоделювати конфлікт, той самий (перший) рядок `app.py` змінено ще й у гілці `main`:

```
$ sed -i '1s/.*/print("Hello, World!")/' app.py
$ git commit -am "Change greeting in main"
[main a3a8e8b] Change greeting in main
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Спроба злиття:

```
$ git merge feature/login
Auto-merging app.py
CONFLICT (content): Merge conflict in app.py
Automatic merge failed; fix conflicts and then commit the result.
```

Стан репозиторію під час конфлікту:

```
$ git status
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)
 
Changes to be committed:
	new file:   login.py
 
Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   app.py
```

Вміст файлу з маркерами конфлікту:

```
$ cat app.py
<<<<<<< HEAD
print("Hello, World!")
=======
print("Hello from login page!")
>>>>>>> feature/login
```

Файл `app.py` було відредаговано вручну: залишено один рядок, маркери видалено:

```
$ cat app.py
print("Hello, World from login page!")
```

Перевірка, що маркерів не залишилось, позначення конфлікту вирішеним і завершення злиття:

```
$ grep -n '<<<<<<<\|>>>>>>>' app.py
$ git add app.py
$ git status
On branch main
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)
 
Changes to be committed:
	modified:   app.py
	new file:   login.py
 
$ GIT_EDITOR=true git merge --continue
[main 49a5063] Merge branch 'feature/login'
```

Історія після злиття:
 
```
$ git log --oneline --graph --all
*   49a5063 (HEAD -> main) Merge branch 'feature/login'
|\
| * 7be8adb (feature/login) Add login feature
* | a3a8e8b Change greeting in main
|/
* 263d32f Add app.py
* a5fc620 Improve project description
* 6abc952 Add usage section
* 3e5962c Add installation section
* 89b38c5 Initial commit: add README
```

### Пояснення

- Конфлікт виник, бо обидві гілки змінили один і той самий рядок різними способами, і Git не може автоматично вибрати правильну версію. Зміни в різних частинах файлу він поєднує сам, а `login.py` додався без проблем (він у розділі `Changes to be committed`).
- У `git status` конфліктний файл позначено `both modified` у розділі `Unmerged paths`.
- Маркери в файлі: між `<<<<<<< HEAD` і `=======` знаходиться версія з поточної гілки (`main`), між `=======` і `>>>>>>> feature/login` версія з гілки, яку зливають. Вирішення конфлікту означає залишити потрібний варіант (або об'єднати обидва) та видалити всі маркери.
- `git add app.py` повідомляє Git, що конфлікт у файлі вирішено, а `git merge --continue` створює merge-коміт `49a5063`. Змінна `GIT_EDITOR=true` лише залишає стандартне повідомлення коміту без відкриття редактора.
- У графі видно розгалуження й подальше зведення двох ліній у merge-коміт із двома батьками.
- Якщо потрібно скасувати злиття, можна виконати `git merge --abort`.
---


## Завдання 5. Перенесення змін за допомогою rebase

**Мета:** усвідомити різницю між merge і rebase, навчитися переписувати історію гілки.

### Хід виконання

Створення гілки `feature/signup` від поточного `main` і три коміти в ній:

```
$ git switch -c feature/signup
Switched to a new branch 'feature/signup'
 
$ echo 'def signup(user): return f"Registered: {user}"' > signup.py
$ git add signup.py
$ git commit -m "Add signup module"
[feature/signup 2ceb56e] Add signup module
 1 file changed, 1 insertion(+)
 create mode 100644 signup.py
 
$ sed -i '1s/.*/print("Sign up to continue")/' app.py
$ git commit -am "Change greeting for signup"
[feature/signup c1c3e4c] Change greeting for signup
 1 file changed, 1 insertion(+), 1 deletion(-)
 
$ echo '# TODO: validate email' >> signup.py
$ git commit -am "Add validation note to signup"
[feature/signup 16309af] Add validation note to signup
 1 file changed, 1 insertion(+)
```

Щоб гілки розійшлись, у `main` зроблено новий коміт, який змінює той самий рядок `app.py`:
 
```
$ git switch main
Switched to branch 'main'
 
$ sed -i '1s/.*/print("Welcome!")/' app.py
$ git commit -am "Change greeting to Welcome"
[main 1479640] Change greeting to Welcome
 1 file changed, 1 insertion(+), 1 deletion(-)
```

**Історія ДО rebase:**

```
$ git log --oneline --graph --all
* 1479640 (HEAD -> main) Change greeting to Welcome
| * 16309af (feature/signup) Add validation note to signup
| * c1c3e4c Change greeting for signup
| * 2ceb56e Add signup module
|/
*   49a5063 Merge branch 'feature/login'
|\
| * 7be8adb (feature/login) Add login feature
* | a3a8e8b Change greeting in main
|/
* 263d32f Add app.py
* a5fc620 Improve project description
* 6abc952 Add usage section
* 3e5962c Add installation section
* 89b38c5 Initial commit: add README
```

Виконання rebase (виникає конфлікт на другому коміті гілки):

```
$ git switch feature/signup
Switched to branch 'feature/signup'
 
$ git rebase main
Auto-merging app.py
CONFLICT (content): Merge conflict in app.py
error: could not apply c1c3e4c... Change greeting for signup
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
Could not apply c1c3e4c... # Change greeting for signup
```

Вміст `app.py` із маркерами конфлікту:

```
$ cat app.py
<<<<<<< HEAD
print("Welcome!")
=======
print("Sign up to continue")
>>>>>>> c1c3e4c (Change greeting for signup)
```

Після ручного виправлення (залишено один рядок, маркери видалено):

```
$ cat app.py
print("Welcome! Sign up to continue")
```

Продовження rebase:

```
$ git add app.py
$ GIT_EDITOR=true git rebase --continue
[detached HEAD 771324d] Change greeting for signup
 1 file changed, 1 insertion(+), 1 deletion(-)
Successfully rebased and updated refs/heads/feature/signup.
```

**Історія ПІСЛЯ rebase:**

```
$ git log --oneline --graph --all
* cab411a (HEAD -> feature/signup) Add validation note to signup
* 771324d Change greeting for signup
* 7f13368 Add signup module
* 1479640 (main) Change greeting to Welcome
*   49a5063 Merge branch 'feature/login'
|\
| * 7be8adb (feature/login) Add login feature
* | a3a8e8b Change greeting in main
|/
* 263d32f Add app.py
* a5fc620 Improve project description
* 6abc952 Add usage section
* 3e5962c Add installation section
* 89b38c5 Initial commit: add README
```

Оскільки `feature/signup` тепер стоїть безпосередньо над `main`, злиття виконується як fast-forward (без створення додаткового merge-коміту):

```
$ git switch main
Switched to branch 'main'
 
$ git merge feature/signup
Updating 1479640..cab411a
Fast-forward
 app.py    | 2 +-
 signup.py | 2 ++
 2 files changed, 3 insertions(+), 1 deletion(-)
 create mode 100644 signup.py
 
$ git log --oneline --graph --all
* cab411a (HEAD -> main, feature/signup) Add validation note to signup
* 771324d Change greeting for signup
* 7f13368 Add signup module
* 1479640 Change greeting to Welcome
*   49a5063 Merge branch 'feature/login'
|\
| * 7be8adb (feature/login) Add login feature
* | a3a8e8b Change greeting in main
|/
* 263d32f Add app.py
* a5fc620 Improve project description
* 6abc952 Add usage section
* 3e5962c Add installation section
* 89b38c5 Initial commit: add README
```

### Пояснення

- `git rebase main` бере коміти гілки `feature/signup`, які відсутні в `main`, і по черзі повторно застосовує їх поверх останнього коміту `main`. Тому гілка ніби «виростає» з нового місця.
- Конфлікт виник під час застосування коміту `c1c3e4c`, бо він змінює той самий рядок, що й новий коміт у `main`. Вирішується він так само, як при merge, але завершується командою `git rebase --continue`. Скасувати rebase можна командою `git rebase --abort`.
- **Хеші комітів змінились**, бо Git створив нові коміти з іншим батьком:
  | Коміт | До rebase | Після rebase |
  |---|---|---|
  | Add signup module | `2ceb56e` | `7f13368` |
  | Change greeting for signup | `c1c3e4c` | `771324d` |
  | Add validation note to signup | `16309af` | `cab411a` |
  Саме це й означає «переписування історії».
- Історія після rebase лінійна: у ній немає розгалуження та merge-коміту для `feature/signup`.
### Порівняння merge і rebase

| | `git merge` | `git rebase` |
|---|---|---|
| Результат | створює merge-коміт із двома батьками | переносить коміти поверх іншої гілки |
| Історія | зберігає реальне розгалуження (див. завдання 4) | лінійна, без розгалужень (див. завдання 5) |
| Існуючі коміти | не змінюються | замінюються новими (змінюються хеші) |
| Конфлікти | вирішуються один раз для всього злиття | можуть виникати на кожному перенесеному коміті |
| Безпека | безпечний для спільних гілок | не можна виконувати для гілок, якими вже користуються інші |

Не слід переписувати історію гілок, які вже опубліковані та використовуються іншими, бо в них розійдуться версії комітів.

---


## Завдання 6. Теги

**Мета:** засвоїти команди git tag -a та git show, зрозуміти, як використовувати теги для релізів.

### Хід виконання

Створення анотованого тегу для поточного стану `main`, перегляд списку тегів та їхніх повідомлень:

```
$ git tag -a v1.0 -m "First stable release"
 
$ git tag
v1.0
 
$ git tag -n
v1.0            First stable release
```

Метадані тегу та коміту, на який він вказує:

```
$ git show v1.0
tag v1.0
Tagger: Valeriia Fedorenko <xaarlox@gmail.com>
Date:   Mon Sep 21 13:41:37 2026 +0300
 
First stable release
 
commit cab411ac4545f7753c64818c1aca1ce8f5ea8acf (HEAD -> main, tag: v1.0, feature/signup)
Author: Valeriia Fedorenko <xaarlox@gmail.com>
Date:   Mon Sep 21 13:35:06 2026 +0300
 
    Add validation note to signup
 
diff --git a/signup.py b/signup.py
index b187ee3..03d4132 100644
--- a/signup.py
+++ b/signup.py
@@ -1 +1,2 @@
 def signup(user): return f"Registered: {user}"
+# TODO: validate email
```

### Пояснення

- Тег `v1.0` позначає конкретний коміт (`cab411a`), який вважається стабільною версією. На відміну від гілки, тег не пересувається при появі нових комітів.
- Прапорець `-a` створює **анотований** тег: це окремий об'єкт у Git, який зберігає автора (`Tagger`), дату та повідомлення. Легкий тег (без `-a`) є лише іменем для коміту без метаданих, тому для релізів рекомендують анотовані.
- `git tag` виводить список тегів, `git tag -n` додає їхні повідомлення.
- `git show v1.0` спочатку показує метадані самого тегу, а потім коміт, на який він вказує, разом зі змінами.
- Теги зручно використовувати для позначення релізів, наприклад щоб повернутись до версії `v1.0` командою `git checkout v1.0` або створити від неї гілку.
---

## Висновки

У ході лабораторної роботи було виконано повний цикл роботи з локальним репозиторієм Git:

1. Ініціалізовано репозиторій і створено перший коміт, розглянуто три стани файлу (`untracked` → `staged` → `committed`).
2. Створено серію комітів з інформативними повідомленнями (заголовок + опис) і переглянуто історію командами `git log`, `git log --oneline`, `git log --stat`, `git log --graph`.
3. Створено гілку `feature/login`, виконано перемикання між гілками та порівняння `git diff main..feature/login`.
4. Змодельовано конфлікт злиття: змінено один і той самий рядок у двох гілках, розглянуто маркери конфлікту, вирішено його вручну й завершено злиття командою `git merge --continue`.
5. Виконано `git rebase main` для гілки `feature/signup`, вирішено конфлікт командою `git rebase --continue`, порівняно історію до й після та встановлено різницю між `merge` і `rebase` (лінійна історія, зміна хешів комітів).
6. Створено анотований тег `v1.0` і переглянуто його метадані командою `git show`.
