## Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```ShellSession
$ open https://cmake.org/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab03** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Выполнить инструкцию учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=<имя_пользователя>
```
Переходим в рабочий каталог
```ShellSession
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
~/Ais105/workspace 
$ source scripts/activate
```
Копируем в локальной репризиторий **lab02** c **github**.com в папку **lab03**
```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03
$ cd projects/lab03
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git
```

```ShellSession
$ g++ -std=c++11 -I./include -c sources/print.cpp
$ ls print.o
print.o
$ nm print.o | grep print
0000000000000098 t _GLOBAL__sub_I__Z5printRKSsRSo
0000000000000000 T _Z5printRKSsRSo
0000000000000027 T _Z5printRKSsRSt14basic_ofstreamIcSt11char_traitsIcEE
$ ar rvs print.a print.o
ar: создаётся print.a
a - print.o
$ file print.a
print.a: current ar archive
$ g++ -std=c++11 -I./include -c examples/example1.cpp
$ ls example1.o
example1.o
$ g++ example1.o print.a -o example1
$ ./example1 && echo
hello
```

```ShellSession
$ g++ -std=c++11 -I./include -c examples/example2.cpp
$ nm example2.o
0000000000000000 b .bss
0000000000000000 d .ctors
0000000000000000 d .data
0000000000000000 p .pdata
0000000000000000 p .pdata$_ZStorSt13_Ios_OpenmodeS_
0000000000000000 r .rdata
0000000000000000 r .rdata$.refptr.__dso_handle
0000000000000000 r .rdata$.refptr._ZNSt8ios_base4InitD1Ev
0000000000000000 r .rdata$zzz
0000000000000000 R .refptr.__dso_handle
0000000000000000 R .refptr._ZNSt8ios_base4InitD1Ev
0000000000000000 t .text
0000000000000000 t .text$_ZStorSt13_Ios_OpenmodeS_
0000000000000000 r .xdata
0000000000000000 r .xdata$_ZStorSt13_Ios_OpenmodeS_
                 U __cxa_atexit
                 U __dso_handle
                 U __gxx_personality_seh0
                 U __main
000000000000014e t _GLOBAL__sub_I_main
                 U _Unwind_Resume
0000000000000104 t _Z41__static_initialization_and_destruction_0ii
                 U _Z5printRKSsRSt14basic_ofstreamIcSt11char_traitsIcEE
                 U _ZNSaIcEC1Ev
                 U _ZNSaIcED1Ev
                 U _ZNSsC1EPKcRKSaIcE
                 U _ZNSsD1Ev
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEEC1EPKcSt13_Ios_Openmode
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEED1Ev
                 U _ZNSt8ios_base4InitC1Ev
                 U _ZNSt8ios_base4InitD1Ev
0000000000000000 r _ZStL19piecewise_construct
0000000000000000 b _ZStL8__ioinit
0000000000000000 T _ZStorSt13_Ios_OpenmodeS_
0000000000000000 T main
$ g++ example2.o print.a -o example2
$ ./example2
$ cat log.txt && echo
hello
```
Удаление файлов
```ShellSession
$ rm -rf example1.o example2.o print.o
$ rm -rf print.a
$ rm -rf example1 example2
$ rm -rf log.txt
```

```ShellSession
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```

```ShellSession
$ cmake -H. -B_build
...
--Build files have been written to: /home/lol/Ais105/workspace/projects/lab03/_build
$ cmake --build _build
Built target print
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```

```ShellSession
$ cmake --build _build
Built target print
$ cmake --build _build --target print
Built target print
$ cmake --build _build --target example1
Built target example1
$ cmake --build _build --target example2
Built target example2
```

```ShellSession
$ ls -la _build/libprint.a
-rw-r--r-- 1 lol lol 2498 мар 30 22:08 _build/libprint.a
$ _build/example1 && echo
hello
$ _build/example2
$ cat log.txt && echo
hello
$ rm -rf log.txt
```

```ShellSession
$ git clone https://github.com/tp-labs/lab03 tmp
$ mv -f tmp/CMakeLists.txt .
$ rm -rf tmp
```

```ShellSession
$ cat CMakeLists.txt
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
-- Build files have been written to: /home/lol/Ais105/workspace/projects/lab03/_build
$ cmake --build _build --target install
$ tree _install
_install
├── cmake
│   ├── print-config.cmake
│   └── print-config-noconfig.cmake
├── include
│   └── print.hpp
└── lib
    └── libprint.a

3 directories, 4 files
```

```ShellSession
$ git add CMakeLists.txt
$ git commit -m"added CMakeLists.txt"
$ git push origin master
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=03
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

**Удачной стажировки!**

## Links
- [Основы сборки проектов на С/C++ при помощи CMake](https://eax.me/cmake/)
- [CMake Tutorial](http://neerc.ifmo.ru/wiki/index.php?title=CMake_Tutorial)
- [C++ Tutorial - make & CMake](https://www.bogotobogo.com/cplusplus/make.php)
- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)

```
Copyright (c) 2015-2019 The ISC Authors
```
