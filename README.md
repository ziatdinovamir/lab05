## Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```sh
$ open https://cmake.org/
```

## Tutorial

Подготовка окружения 
```sh
$ export GITHUB_USERNAME=<имя_пользователя>
```

```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

Клонирование кода из репозитория lab02 в папку projects/lab03
```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03
```
```sh
Cloning into 'projects/lab03'...
remote: Enumerating objects: 25, done.
remote: Counting objects: 100% (25/25), done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 25 (delta 6), reused 9 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (25/25), 12.69 KiB | 282.00 KiB/s, done.
Resolving deltas: 100% (6/6), done.
```
Видим, что прошло успешно

Затем идет удаление привязки к старому репозиторию и добавление этой к привязки к новому
```sh
$ cd projects/lab03
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git
```
Далее компилируем файл библиотеки sources/print.cpp в объектный файл
```sh
$ g++ -std=c++11 -I./include -c sources/print.cpp
$ ls print.o
```
Смотрим список символов (в объектном файле и фильтруем (grep), чтобы найти функцию print.
```sh
$ nm print.o | grep print
```
```sh
0000000000000000 T _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSo
000000000000002a T _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSt14basic_ofstreamIcS2_E
```
```sh
$ ar rvs print.a print.o
$ file print.a
```
Компиляция и запуск первого примера
```sh
$ g++ -std=c++11 -I./include -c examples/example1.cpp
$ ls example1.o
$ g++ example1.o print.a -o example1
$ ./example1 && echo
```
Видим `hello` , значит все прошло успешно
```sh
$ g++ -std=c++11 -I./include -c examples/example2.cpp
$ nm example2.o
$ g++ example2.o print.a -o example2
$ ./example2
$ cat log.txt && echo
```
Также видим `hello`

Далее выполняем очистку. При этом даляются все файлы, созданные вручную и остается только исходный код
```sh
$ rm -rf example1.o example2.o print.o
$ rm -rf print.a
$ rm -rf example1 example2
$ rm -rf log.txt
```
Создание CMakeLists.txt
```sh
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```
И сборка 
```sh
$ cmake -H. -B_build
$ cmake --build _build
```
В CMakeLists.txt добавляются инструкции по созданию двух исполняемых файлов (example1 и example2)
```sh
$ cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```
Финальная сборка и тестирование через CMake
```sh
$ cmake --build _build
$ cmake --build _build --target print
$ cmake --build _build --target example1
$ cmake --build _build --target example2
```

```sh
$ ls -la _build/libprint.a
$ _build/example1 && echo
hello
$ _build/example2
$ cat log.txt && echo
hello
$ rm -rf log.txt
```
Замена на CMake-файл из лабораторной работы
```sh
$ git clone https://github.com/tp-labs/lab03 tmp
$ mv -f tmp/CMakeLists.txt .
$ rm -rf tmp
```
Установка проекта 
```sh
$ cat CMakeLists.txt
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
$ cmake --build _build --target install
$ tree _install
```
Сохранение результата в Git
```sh
$ git add CMakeLists.txt
$ git commit -m"added CMakeLists.txt"
$ git push origin master
```

## Report

```sh
$ popd
$ export LAB_NUMBER=03
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

Сначала создадим директории для каждого  из заданий
```sh
mkdir -p formatter_lib formatter_ex_lib hello_world solver solver/solver_lib
```
```sh
projects/

├── formatter_lib/

├── formatter_ex_lib/

├── hello_world/

└── solver/ 

    └── solver_lib/  
```
Создаем `CMakeLists.txt` в папке `formatter_lib`, а так же основной и заголовочные файлы
```sh
cd ~/ziatdinovamir/workspace/projects/formatter_lib
cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.4)
project(formatter)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter STATIC formatter.cpp)
EOF
```
```sh
cat > formatter.cpp << 'EOF'
#include "formatter.h"

std::string formatter(const std::string& message)
{
    std::string res;
    res += "-------------------------\n";
    res += message + "\n";
    res += "-------------------------\n";
    return res;
}
EOF
```
Затем собираем наш проект 
```sh
cmake -H. -B_build
cmake --build _build
```
Увидим 
```sh
[ 50%] Building CXX object CMakeFiles/formatter.dir/formatter.cpp.o
[100%] Linking CXX static library libformatter.a
[100%] Built target formatter
```
### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.
```sh
cd ~/ziatdinovamir/workspace/projects/formatter_ex_lib
cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.4)
project(formatter_ex)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter_ex STATIC formatter_ex.cpp)

target_include_directories(formatter_ex PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib)
target_link_libraries(formatter_ex formatter)
EOF
```
```sh
cat > formatter_ex.h << 'EOF'
#pragma once

#include <string>
#include <iostream>

std::ostream& formatter(std::ostream& out, const std::string& message);
EOF
```
```sh
cat > formatter_ex.cpp << 'EOF'
#include "formatter_ex.h"

#include "formatter.h"

std::ostream& formatter(std::ostream& out, const std::string& message)
{
    return out << formatter(message);
}
EOF
```
И также осуществляем сборку
```sh
cmake -H. -B_build
```
```sh
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/amir/ziatdinovamir/workspace/projects/formatter_ex_lib/_build
```
```sh
cmake --build _build
```
```sh
[ 50%] Building CXX object CMakeFiles/formatter_ex.dir/formatter_ex.cpp.o
[100%] Linking CXX static library libformatter_ex.a
[100%] Built target formatter_ex
```

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.
* 
```sh
cd ~/ziatdinovamir/workspace/projects/hello_world
cmake_minimum_required(VERSION 3.10)
project(hello_world)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib
    ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib
)

add_subdirectory(../formatter_lib formatter_lib)
add_subdirectory(../formatter_ex_lib formatter_ex_lib)

add_executable(hello_world hello_world.cpp)
target_link_libraries(hello_world formatter_ex)
```
```sh
cat > formatter_ex.h << 'EOF'
#pragma once

#include <string>
#include <iostream>

std::ostream& formatter(std::ostream& out, const std::string& message);
EOF
```
```sh
cat > hello_world.cpp << 'EOF'
#include <iostream>

#include "formatter_ex.h"

int main()
{
    formatter(std::cout, "hello, world!");
}
EOF
```
Далее также как в прошлых пунктах осуществляем сборку
```sh
cmake -H. -B_build
```
```sh
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/amir/ziatdinovamir/workspace/projects/hello_world/_build
```
```sh
cmake --build _build
```
```sh
CMake Deprecation Warning at /home/amir/ziatdinovamir/workspace/projects/formatter_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Deprecation Warning at /home/amir/ziatdinovamir/workspace/projects/formatter_ex_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/amir/ziatdinovamir/workspace/projects/hello_world/_build
[ 16%] Building CXX object formatter_lib/CMakeFiles/formatter.dir/formatter.cpp.o
[ 33%] Linking CXX static library libformatter.a
[ 33%] Built target formatter
[ 50%] Building CXX object formatter_ex_lib/CMakeFiles/formatter_ex.dir/formatter_ex.cpp.o
[ 66%] Linking CXX static library libformatter_ex.a
[ 66%] Built target formatter_ex
[ 83%] Building CXX object CMakeFiles/hello_world.dir/hello_world.cpp.o
[100%] Linking CXX executable hello_world
[100%] Built target hello_world
```
И запускаем
```sh
_build/hello_world
```
И видим прекрасный результат
```sh
-------------------
=== Hello, World! ===
-------------------
```
Далее приступаем к solver
```sh
cd ~/ziatdinovamir/workspace/projects/solver/solver_lib
cmake_minimum_required(VERSION 3.10)
project(solver_lib)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(solver_lib STATIC solver.cpp)
```
```sh
cat > solver.h << 'EOF'
#pragma once

void solve(float a, float b, float c, float& x1, float& x2);
EOF
```
```sh
cat > solver.cpp << 'EOF'
#include "solver.h"

#include <stdexcept>

void solve(float a, float b, float c, float& x1, float& x2)
{
    float d = (b * b) - (4 * a * c);

    if (d < 0)
    {
        throw std::logic_error{"error: discriminant < 0"};
    }

    x1 = (-b - std::sqrtf(d)) / (2 * a);
    x2 = (-b + std::sqrtf(d)) / (2 * a);
}
EOF
```
Затем проваливаемся на одну степнь вниз
```sh
cd ..
cmake_minimum_required(VERSION 3.10)
project(solver)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/solver_lib
    ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib
    ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib
)

add_subdirectory(solver_lib)
add_subdirectory(../formatter_lib formatter_lib)
add_subdirectory(../formatter_ex_lib formatter_ex_lib)

add_executable(solver equation.cpp)
target_link_libraries(solver solver_lib formatter formatter_ex)
```
```sh
cat > equation.cpp << 'EOF'
#include <iostream>

#include "formatter_ex.h"
#include "solver.h"

int main()
{
    float a = 0;
    float b = 0;
    float c = 0;

    std::cin >> a >> b >> c;

    float x1 = 0;
    float x2 = 0;

    try
    {
        solve(a, b, c, x1, x2);

        formatter(std::cout, "x1 = " + std::to_string(x1));
        formatter(std::cout, "x2 = " + std::to_string(x2));
    }
    catch (const std::logic_error& ex)
    {
        formatter(std::cout, ex.what());
    }

    return 0;
}
EOF
```
Также собираем 
```sh
cmake -H. -B_build
```
```sh
-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
CMake Deprecation Warning at /home/amir/ziatdinovamir/workspace/projects/formatter_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Deprecation Warning at /home/amir/ziatdinovamir/workspace/projects/formatter_ex_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- Configuring done (0.3s)
-- Generating done (0.0s)
-- Build files have been written to: /home/amir/ziatdinovamir/workspace/projects/solver/_build
```
```sh
cmake --build _build
```
```sh
[ 25%] Built target formatter
[ 50%] Built target formatter_ex
[ 62%] Building CXX object solver_lib/CMakeFiles/solver_lib.dir/solver.cpp.o
[ 75%] Linking CXX static library libsolver_lib.a
[ 75%] Built target solver_lib
[ 87%] Building CXX object CMakeFiles/solver.dir/equation.cpp.o
[100%] Linking CXX executable solver
[100%] Built target solver
```
И запускаем, прописывая аргументы
```sh
_build/solver
1 -3 2
```
И наблюдаем прекрасный результат
```sh
-------------------
=== x1 = 1.000000 ===
-------------------

-------------------
=== x2 = 2.000000 ===
-------------------
```
