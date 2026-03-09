## Laboratory work II

Данная лабораторная работа посвещена изучению систем контроля версий на примере **Git**.

```bash
$ open https://git-scm.com
```

## Tutorial

Создаем переменные окружения и псевдоним функции

```sh
$ export GITHUB_USERNAME=<имя_пользователя>
$ export GITHUB_EMAIL=<адрес_почтового_ящика>
$ export GITHUB_TOKEN=<сгенирированный_токен>
$ alias edit=<nano|vi|vim|subl>
```
Переходим в директорию и выполняем скрипт
```sh
$ cd ${GITHUB_USERNAME}/workspace
$ source scripts/activate
```
Создаем католог в домашней папке, создаем файл и записываем в него элементы конфигурации, затем устанавливаем её
```sh
$ mkdir ~/.config
$ cat > ~/.config/hub <<EOF
github.com:
- user: ${GITHUB_USERNAME}
  oauth_token: ${GITHUB_TOKEN}
  protocol: https
EOF
$ git config --global hub.protocol https
```
Создаем папку и в ней инициализирусем git
```sh
$ mkdir projects/lab02 && cd projects/lab02
$ git init
```
```sh
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint: 
hint:  git config --global init.defaultBranch <name>
hint: 
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint: 
hint:  git branch -m <name>
Initialized empty Git repository in /home/amir/ziatdinovamir/workspace/.git/
```
Устанавливаем конфигурацию, используя заранее созданный конфиг
```sh
$ git config --global user.name ${GITHUB_USERNAME}
$ git config --global user.email ${GITHUB_EMAIL}
# check your git global settings
$ git config -e --global
```
Привязываем удаленный репозиторий к локальному с именем origin и совмещаем их
```sh
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab02.git
$ git pull origin master
```
Добавляем файл, смотрим изменения, добавляем изменения в индекс, комитим и отправляем на удаленный репозиторий
```sh
$ touch README.md
$ git status
$ git add README.md
$ git commit -m"added README.md"
$ git push origin master
```

Добавляем на сервисе **GitHub** в репозитории **lab02** файл **.gitignore**
со следующем содержимом:
```sh
*build*/
*install*/
*.swp
.idea/
```
Собираем все изменения, которые появились на GitHub и обновляем локальную копию. Далее смотрим историю коммитов
```sh
$ git pull origin master
$ git log
```
Создаем структуру:
lab02/

├── sources/

├── include/

├── examples/

└── README.md
```sh
$ mkdir sources
$ mkdir include
$ mkdir examples
```
Далее создаем реализацию функций
```sh
$ cat > sources/print.cpp <<EOF
#include <print.hpp>

void print(const std::string& text, std::ostream& out)
{
  out << text;
}

void print(const std::string& text, std::ofstream& out)
{
  out << text;
}
EOF
```
И затем собственно сам код на С++
```sh
$ cat > include/print.hpp <<EOF
#include <fstream>
#include <iostream>
#include <string>

void print(const std::string& text, std::ofstream& out);
void print(const std::string& text, std::ostream& out = std::cout);
EOF
```
Создаем функцию для вывода в консоль
```sh
$ cat > examples/example1.cpp <<EOF
#include <print.hpp>

int main(int argc, char** argv)
{
  print("hello");
}
EOF
```
Код для вывода в файл
```sh
$ cat > examples/example2.cpp <<EOF
#include <print.hpp>

#include <fstream>

int main(int argc, char** argv)
{
  std::ofstream file("log.txt");
  print(std::string("hello"), file);
}
EOF
```
Редактируем README.md
```sh
$ edit README.md
```
Проверка и коммит
```sh
$ git status
$ git add .
$ git commit -m"added sources"
$ git push origin master
```
Создаем папку с копией лабораторной работы, редайктируем его в редакторе и отправляем.
```sh
$ cd ~/workspace/
$ export LAB_NUMBER=02
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER}.git tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

### Part I

1. Создаем пустой репозиторий на сервисе github.com
2. Выполняем инструкцию по созданию первого коммита на странице репозитория, созданного на предыдещем шаге.

```sh
echo "# lab02-part1" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/ziatdinovamir/lab02-part1.git
git push -u origin main
```
После чего видим результат, что первый коммит был успешно создан.

```sh
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 228 bytes | 228.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/ziatdinovamir/lab02-part1.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

3. Создаем файл `hello_world.cpp` в локальной копии репозитория (который появился на шаге 2). Реализуем программу **Hello world** на языке C++ используя плохой стиль кода. После заголовочных файлов вставим строку `using namespace std;`.
```sh
cat > hello_world.cpp <<EOF
#include <iostream>
using namespace std;
                    
int main() {
    cout << "Hello, World!" << endl;
    return 0;                       
}            
EOF
```
4. Добавляем этот файл в локальную копию репозитория.

```sh
git add hello_world.cpp
```
Также можно сделать проверку на то, что файл был успешно добавлен
```sh
git status
```
На экране должно появиться:
```sh
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   hello_world.cpp

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	REPORT.md
```
5. Закоммитим изменения с *осмысленным* сообщением.
```sh
git commit -m "Program with poor coding style"
```
После чего увидим на экране:
```sh
[main 5fb8dab] Program with poor coding style
 1 file changed, 7 insertions(+)
 create mode 100644 hello_world.cpp
```
6. Изменим исходный код так, чтобы программа через стандартный поток ввода запрашивалось имя пользователя. А в стандартный поток вывода печаталось сообщение `Hello world from name`, где `name` имя пользователя.
```sh
cat > hello_world.cpp <<EOF
#include <iostream>
#include <string>
using namespace std;

int main() {
    string name;
    cout << "Enter your name: " << endl;
    getline(cin, name);
    cout << "Hello world from " << name << endl;
    return 0;
}
EOF
```
7. Закоммитим новую версию программы. Почему не надо добавлять файл повторно `git add`?
```sh
git commit -am "Code with UserName"
[main a036d27] Code with UserName
 1 file changed, 5 insertions(+), 1 deletion(-)
```
При этом флаг `-a` автоматически добавляет все отслеживаемые файлы, которые были изменены.

8. Запушим изменения в удалёный репозиторий.
```sh
git push origin main
```
Видим, что все прошло успешно
```sh
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 4 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 809 bytes | 809.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/ziatdinovamir/lab02-part1.git
   3c842d0..a036d27  main -> main
```

9. Проверяем, что история коммитов доступна в удалёный репозитории. Для этого на странице нашего репозитория открываем вкладку commit, где видим все 3 наших изменения.

1) first commit
```sh
# lab02-part1
```

2)Program with poor coding style
```sh
#include <iostream>

using namespace std;

int main() {
    cout << "Hello, World!" << endl;
    return 0;
}
```

3)Code with UserName
```sh
#include <iostream>
#include <string>

using namespace std;

int main() {
    cout << "Hello, World!" << endl;
    string name;
    cout << "Enter your name: " << endl;
    getline(cin, name);
    cout << "Hello world from " << name << endl;
    return 0;
}
```

### Part II

**Note:** *Работать продолжаем с теми же репоззиториями, что и в первой части задания.*
1. В локальной копии репозитория создаем локальную ветку `patch1`.
```sh
git checkout -b patch1
```
Видим, что все успешно
```sh
Switched to a new branch 'patch1'
```

2. Внесом изменения в ветке `patch1` по исправлению кода и избавляемся от `using namespace std;`.
```sh
cat > hello_world.cpp <<EOF
#include <iostream>
#include <string>

int main() {
    std::string name;
    std::cout << "Enter your name: " << std::endl;
    std::getline(std::cin, name);
    std::cout << "Hello world from " << name << std::endl;
    return 0;
}
EOF

```

3. Закоммитим локальную ветку в удалённый репозиторий.
```sh
git commit -am  "Code without 'using namepace std'"
```
Увидим:
```sh
[patch1 5b9d3ac] Code without 'using namepace std'
 1 file changed, 4 insertions(+), 5 deletions(-)
```
Далее запушим эту же ветку
```sh
git push -u origin patch1
```
```sh
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 444 bytes | 444.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
remote: 
remote: Create a pull request for 'patch1' on GitHub by visiting:
remote:      https://github.com/ziatdinovamir/lab02-part1/pull/new/patch1
remote: 
To https://github.com/ziatdinovamir/lab02-part1.git
 * [new branch]      patch1 -> patch1
branch 'patch1' set up to track 'origin/patch1'.
```

4. Проверим, что ветка `patch1` доступна в удалёный репозитории.
```sh
git branch -r
```
```sh
  origin/main
  origin/patch1
```
Как видим ветка действительно появилась на GitHub

5. Создаем на GitHub pull-request.
6. В локальной копии в ветке `patch1` добавьте в исходный код комментарии.
```sh
nano hello_world.cpp
```
Далее пишем комментарии лдя нашего кода
```sh
#include <iostream>
#include <string>

int main() {
    std::string name;//Users's name. 
    std::cout << "Enter your name: " << std::endl;//User enters his name
    std::getline(std::cin, name);//Reading the string with spaces
    std::cout << "Hello world from " << name << std::endl;//Display the inscrip>
    return 0;
}
```
Сохраняем изменения и выходим

7. Коммитим изменеия 
```sh
git commit -am "Code with comments"
```
```sh
[patch1 9af7d15] Code with comments
 1 file changed, 4 insertions(+), 4 deletions(-)
```
 И отправляем их на GitHub.
 
```sh
 git push origin patch1
```
Отправка прошла успешно
```sh
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 490 bytes | 490.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/ziatdinovamir/lab02-part1.git
   5b9d3ac..9af7d15  patch1 -> patch1
```
 
8. Проверяем, что новые изменения есть в созданном на **шаге 5** pull-request
У нас всего 5 изменения, 3 из них были представлены выше:

4)Code without 'using namepace std'
```sh
#include <iostream>
#include <string>

int main() {
    std::string name; 
    std::cout << "Enter your name: " << std::endl;
    std::getline(std::cin, name);
    std::cout << "Hello world from " << name << std::endl;
    return 0;
}
```
5)Code with comments
```sh
#include <iostream>
#include <string>
int main() {
    std::string name;//Users's name.
    std::cout << "Enter your name: " << std::endl;//User enters his name
    std::getline(std::cin, name);//Reading the string with spaces
    std::cout << "Hello world from " << name << std::endl;//Display the inscription with  User's name
    return 0;
}
```
9. В удалённом репозитории выполним слияние PR `patch1 -> main` и удаляем ветку `patch1` в удаленном репозитории.
10. Локально выполняем **pull**.

Переключаемся в main
```sh
git checkout main
```
```sh
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
```
И выполняем **pull**
```sh
git pull origin main 
```
```sh
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (1/1), 906 bytes | 906.00 KiB/s, done.
From https://github.com/ziatdinovamir/lab02-part1
 * branch            main       -> FETCH_HEAD
   a036d27..385b6c2  main       -> origin/main
Updating a036d27..385b6c2
Fast-forward
 hello_world.cpp | 9 ++++-----
 1 file changed, 4 insertions(+), 5 deletions(-)
```
11. С помощью команды **git log** просмотрим историю в локальной версии ветки `master`.
```sh
git log
```
И увидим результат:
```sh
commit 385b6c21f3a5a620d8501f26c0792e2adadf5f49 (HEAD -> main, origin/main)
Merge: a036d27 9af7d15
Author: ziatdinovamir <ziatdinov.amir.07@gmail.com>
Date:   Tue Mar 3 12:27:37 2026 +0300

    Merge pull request #1 from ziatdinovamir/patch1
    
    Code without 'using namepace std'

commit 9af7d150fe604feacd2e1f31cbf0cefa22e95c45 (origin/patch1, patch1)
Author: ziatdinovamir <ziatdinov.amir.07@gmail.com>
Date:   Tue Mar 3 12:00:53 2026 +0300

    Code with comments

commit 5b9d3ace02d5f5a1c51d4359714adb2382d8c130
Author: ziatdinovamir <ziatdinov.amir.07@gmail.com>
Date:   Tue Mar 3 11:38:14 2026 +0300

    Code without 'using namepace std'

commit a036d277c721b150e8ef1a21bf14037467a50223
Author: ziatdinovamir <ziatdinov.amir.07@gmail.com>
Date:   Tue Mar 3 11:09:17 2026 +0300

    Code with UserName

commit 5fb8dab419f5944b952d79bbb96796dd3a87de55
Author: ziatdinovamir <ziatdinov.amir.07@gmail.com>
Date:   Tue Mar 3 11:03:43 2026 +0300

    Program with poor coding style

commit 3c842d08de5fd2f2a3993f0f0df0bf9eb36143f3
Author: ziatdinovamir <ziatdinov.amir.07@gmail.com>
Date:   Tue Mar 3 10:47:36 2026 +0300

    first commit
(END)
```
12. Удаляем локальную ветку `patch1`.
```sh
git branch -d patch1
```
Видим, что удаление прошло успешно
```sh
Deleted branch patch1 (was 9af7d15
```

### Part III

**Note:** *Работать продолжайте с теми же репоззиториями, что и в первой части задания.*
1. Создаем новую локальную ветку `patch2`.
```sh
git checkout -b patch2
```
2. Изменим *code style* с помощью утилиты [**clang-format**], используя `-style=Mozilla`.
```sh
sudo apt install clang-format
```
```sh
[sudo] password for amir: 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  clang-format-18
The following NEW packages will be installed:
  clang-format clang-format-18
0 upgraded, 2 newly installed, 0 to remove and 177 not upgraded.
Need to get 59,7 kB of archives.
After this operation, 269 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://ru.archive.ubuntu.com/ubuntu noble-updates/universe amd64 clang-format-18 amd64 1:18.1.3-1ubuntu1 [53,8 kB]
Get:2 http://ru.archive.ubuntu.com/ubuntu noble/universe amd64 clang-format amd64 1:18.0-59~exp2 [5 970 B]
Fetched 59,7 kB in 0s (540 kB/s)        
Selecting previously unselected package clang-format-18.
(Reading database ... 156596 files and directories currently installed.)
Preparing to unpack .../clang-format-18_1%3a18.1.3-1ubuntu1_amd64.deb ...
Unpacking clang-format-18 (1:18.1.3-1ubuntu1) ...
Selecting previously unselected package clang-format:amd64.
Preparing to unpack .../clang-format_1%3a18.0-59~exp2_amd64.deb ...
Unpacking clang-format:amd64 (1:18.0-59~exp2) ...
Setting up clang-format-18 (1:18.1.3-1ubuntu1) ...
Setting up clang-format:amd64 (1:18.0-59~exp2) ...
Processing triggers for man-db (2.12.0-4build2) ...
```
```sh
clang-format -style=Mozilla -i hello_world.cpp
```
```sh
cat hello_world.cpp
```
```sh
#include <iostream>
#include <string>

int
main()
{
  std::string name;                 // Users's name.
  std::cout << "Enter your name: " << std::endl; // User enters his name
  std::getline(std::cin, name);     // Reading the string with spaces
  std::cout << "Hello world from " << name
            << std::endl; // Display the inscription with  User's name
  return 0;
}
```
Как видно, всё получилось

3. **commit**, **push**, создаем pull-request `patch2 -> master`.
Закоммитим 
```sh
git commit -am "Apply Mozilla code style"
```
```sh
[patch2 ce753f4] Apply Mozilla code style
 1 file changed, 9 insertions(+), 6 deletions(-)
```
Запушим
```sh
git push origin patch2
```
```sh
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 428 bytes | 428.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
remote: 
remote: Create a pull request for 'patch2' on GitHub by visiting:
remote:      https://github.com/ziatdinovamir/lab02-part1/pull/new/patch2
remote: 
To https://github.com/ziatdinovamir/lab02-part1.git
 * [new branch]      patch2 -> patch2
branch 'patch2' set up to track 'origin/patch2'.
```
И создаем pull-request

4. В ветке **master** в удаленном репозитории изменим комментарии, добавив :)))))
Переключаемся на main
```sh
git checkout main
```
```sh
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
```
```sh
cat > hello_world.cpp <<EOF
#include <iostream>
#include <string>
int main() {
    std::string name;//Users's name.:)))))
    std::cout << "Enter your name: " << std::endl;//User enters his name:)))))     
    std::getline(std::cin, name);//Reading the string with spaces:)))))
    std::cout << "Hello world from " << name << std::endl;//Display the inscription with  User's name :)))))
    return 0;
}
EOF
```
```sh
git commit -am "Update comments with  :)))))"
```
```sh
[main 0150834] Update comments with  :)))))
 1 file changed, 4 insertions(+), 4 deletions(-)
```
```sh
git push origin main
```
```sh
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 365 bytes | 365.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/ziatdinovamir/lab02-part1.git
   385b6c2..0150834  main -> main
```
5. Убедимся, что в pull-request появились *конфликтны*.
Видим сообщение: **This branch has conflicts that must be resolved**
6. Для этого локально выполним **pull** + **rebase**. **Исправим конфликты**.
Переключаемся на patch2
```sh
git checkout patch2
```
Увидим:
```sh
Switched to branch 'patch2'
Your branch is up to date with 'origin/patch2'.
```
Скачаем изменения из main и выполним rebase.
```sh
git fetch origin
```
```sh
From https://github.com/ziatdinovamir/lab02-part1
 * [new branch]      master     -> origin/master
```
```sh
git rebase origin/main
```
```sh
Auto-merging hello_world.cpp
CONFLICT (content): Merge conflict in hello_world.cpp
error: could not apply ce753f4... Apply Mozilla code style
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
Could not apply ce753f4... Apply Mozilla code style
```
```sh
nano hello_world.cpp
```
Отредактируем наш код

7. Сделаем *force push* в ветку `patch2`
Завершаем коммит
```sh
git add hello_world.cpp
```
Исправив конфликты продолжаем rebase
```sh
git rebase --continue
```
```sh
[detached HEAD 0e43bf6] Apply Mozilla code style
 1 file changed, 10 insertions(+), 6 deletions(-)
Successfully rebased and updated refs/heads/patch2
```
И делаем force push
```sh
git push --force-with-lease origin patch2
```
После чего увидим на экране
```sh
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 509 bytes | 509.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/ziatdinovamir/lab02-part1.git
 + ce753f4...0e43bf6 patch2 -> patch2 (forced update)
```
8. Убеждаемся, что в pull-request пропали конфликты и видим надпись :**No conflicts with base branch** 
9. В удалённом репозитории выполним слияние PR `patch2 -> main` и удаляем ветку `patch1` в удаленном репозитории.
# lab03
