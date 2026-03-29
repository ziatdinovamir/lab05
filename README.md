<фрагмент_вставки_значка>
[![Build Status](https://app.travis-ci.com/ziatdinovamir/lab04.svg?branch=main)](https://app.travis-ci.com/ziatdinovamir/lab04)

## Laboratory work IV

Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса **Travis CI**

```sh
$ open https://travis-ci.org
```
## Tutorial
Подготавливаем переменные окружения
```sh
$ export GITHUB_USERNAME=<имя_пользователя>
$ export GITHUB_TOKEN=<полученный_токен>
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```
Скачиваем и устанавливаем RVM — менеджер версий Ruby.
```sh
$ \curl -sSL https://get.rvm.io | bash -s -- --ignore-dotfiles
```
```sh
Turning on ignore dotfiles mode.
Downloading https://github.com/rvm/rvm/archive/master.tar.gz
Installing RVM to /home/amir/.rvm/
Installation of RVM in /home/amir/.rvm/ is almost complete:

  * To start using RVM you need to run `source /home/amir/.rvm/scripts/rvm`
    in all your open shell windows, in rare cases you need to reopen all shell windows.
Thanks for installing RVM 🙏
Please consider donating to our open collective to help us maintain RVM.

👉  Donate: https://opencollective.com/rvm/donate
```
```sh
$ echo "source $HOME/.rvm/scripts/rvm" >> scripts/activate
$ . scripts/activate
$ rvm autolibs disable
$ rvm install ruby-2.4.2
$ rvm use 2.4.2 --default
$ gem install travis
```
Подготавливаем репозитори1 lab04, клонируя lab03
```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab04
$ cd projects/lab04
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04
```
Создаем `.travis.yml`
```sh
$ cat > .travis.yml <<EOF
language: cpp
EOF
```

```sh
$ cat >> .travis.yml <<EOF

script:
- cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
- cmake --build _build
- cmake --build _build --target install
EOF
```

```sh
$ cat >> .travis.yml <<EOF

addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
EOF
```
Авторизуемся в Travis CI используя GitHub токен
```sh
$ travis login --github-token ${GITHUB_TOKEN}
```
```sh
Successfully logged in as ziatdinovamir!
```
```sh
$ travis lint
```
```sh
travis lint
Hooray, .travis.yml looks valid :)
```
Добавление бейджа в `README`
```sh
$ ex -sc '1i|<фрагмент_вставки_значка>' -cx README.md
```
Добавляем файлы в Git, коммитим и пушим на GitHub
```sh
$ git add .travis.yml
$ git add README.md
$ git commit -m"added CI"
$ git push origin master
```
Команды Travis CLI для управления:

Проверяем файлы после изменений 
```sh
$ travis lint
```
Выводим список аккаунтов, к которым у нас есть доступ
```sh
$ travis accounts
```
Синхронизируем ваш с GitHub 
```sh
$ travis sync
```
Выводим список репозиториев на Travis CI
```sh
$ travis repos
```
Включаем текущий репозиторий (lab04) в Travis CI
```sh
$ travis enable
```
Выводим последние сборки репозиториев
```sh
$ travis whatsup
```
Показывает статус сборок для разных веток репозитория
 ```sh
$ travis branches
```
Выводим историю последних сборок текущего репозитория
```sh
$ travis history
```
Выводим подробную информацию о последней сборке
```sh
$ travis show
```

## Report

```sh
$ popd
$ export LAB_NUMBER=04
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

Вы продолжаете проходить стажировку в "Formatter Inc." (см [подробности](https://github.com/tp-labs/lab03#Homework)).

В прошлый раз ваше задание заключалось в настройке автоматизированной системы **CMake**.

Сейчас вам требуется настроить систему непрерывной интеграции для библиотек и приложений, с которыми вы работали в [прошлый раз](https://github.com/tp-labs/lab03#Homework). Настройте сборочные процедуры на различных платформах:
* используйте [TravisCI](https://travis-ci.com/) для сборки на операционной системе **Linux** с использованием компиляторов **gcc** и **clang**;
* используйте [AppVeyor](https://www.appveyor.com/) для сборки на операционной системе **Windows**.

Для этого в корне репозитория создаем `.travis.yml` и `appveyor.yml`. 
```sh
cd ~/ziatdinovamir/workspace/projects/lab04

cat > .travis.yml << 'EOF'
language: cpp

script:
- cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
- cmake --build _build
- cmake --build _build --target install

addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
EOF

cat > appveyor.yml << 'EOF'
image: Visual Studio 2019
build_script:
- for /d %%i in (formatter_lib formatter_ex_lib hello_world solver) do (cd %%i && mkdir build && cd build && cmake .. && cmake --build . && cd ..\..)
EOF
```
После чего коммитим и пушим каждый из файлов:

`.travis.yml`
```sh
git add .travis.yml
git commit -m "Add Travis CI configuration"
git push origin main
```
`appveyor.yml`
```sh
git add appveyor.yml
git commit -m "Add AppVeyor CI configuration"
git push origin main
```
После всех операций запускается сборка. Как видим все прошло успешно.
```sh
Build started
git clone -q --branch=main https://github.com/ziatdinovamir/lab04.git C:\projects\lab04-8hi7m
git checkout -qf 54270f192b15f78808e3bb48a2f3619a495d8ae4
cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
-- Building for: Visual Studio 16 2019
-- Selecting Windows SDK version 10.0.26100.0 to target Windows 10.0.17763.
-- The C compiler identification is MSVC 19.29.30159.0
-- The CXX compiler identification is MSVC 19.29.30159.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done (39.6s)
-- Generating done (0.0s)
-- Build files have been written to: C:/projects/lab04-8hi7m/_build
cmake --build _build
Microsoft (R) Build Engine version 16.11.6+a918ceb31 for .NET Framework
Copyright (C) Microsoft Corporation. All rights reserved.
  1>Checking Build System
  Building Custom Rule C:/projects/lab04-8hi7m/CMakeLists.txt
  print.cpp
  print.vcxproj -> C:\projects\lab04-8hi7m\_build\Debug\print.lib
  Building Custom Rule C:/projects/lab04-8hi7m/CMakeLists.txt
cmake --build _build --target install
Microsoft (R) Build Engine version 16.11.6+a918ceb31 for .NET Framework
Copyright (C) Microsoft Corporation. All rights reserved.
  print.vcxproj -> C:\projects\lab04-8hi7m\_build\Debug\print.lib
  1>
  -- Install configuration: "Debug"
  -- Installing: C:/projects/lab04-8hi7m/_install/lib/print.lib
  -- Installing: C:/projects/lab04-8hi7m/_install/include
  -- Installing: C:/projects/lab04-8hi7m/_install/include/print.hpp
  -- Installing: C:/projects/lab04-8hi7m/_install/cmake/print-config.cmake
  -- Installing: C:/projects/lab04-8hi7m/_install/cmake/print-config-debug.cmake
Discovering tests...OK
Build success
```

Но так как travis платный, используем GitHub actions. Во вкладке actions видим, что сборка прошла успешно

[![Build Status](https://app.travis-ci.com/ziatdinovamir/lab04.svg?token=F7dmjxSRNLcRTbHkuMDh&branch=main)](https://app.travis-ci.com/ziatdinovamir/lab04)
>>>>>>> 16d416fa09d8f9e67b2bf2ee22205c84f1782122
# CI/CD Pipeline
