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
#Положить текущую директорию в стек, чтобы позже вернуться к ней с помощью popd
$ pushd .
~/Ais105/workspace 
$ source scripts/activate
```
Копируем в локальной репризиторий **lab02** c **github**.com в папку **lab03**
```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03
$ cd projects/lab03
#Удвлить все ветки удаленного отслеживания удаляются
$ git remote remove origin
#Подключение локального репозитория к удаленному серверу
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git
```

```ShellSession
# Компиляция исходного файла со стандартом С++ 2011 года и создание объектного файла
$ g++ -std=c++11 -I./include -c sources/print.cpp
# Проверка существования файла print.o
$ ls print.o
print.o
# Печать таблицы имен объектного файла, содержащих в тексте комбинацию print
$ nm print.o | grep print
0000000000000098 t _GLOBAL__sub_I__Z5printRKSsRSo
0000000000000000 T _Z5printRKSsRSo
0000000000000027 T _Z5printRKSsRSt14basic_ofstreamIcSt11char_traitsIcEE
# Cоздание файла с расширением *.a (файл статистической библиотеки)
$ ar rvs print.a print.o
ar: создаётся print.a
a - print.o
# Отображение типа файла
$ file print.a
print.a: current ar archive
$ g++ -std=c++11 -I./include -c examples/example1.cpp
# Проверка существования файла example1.o
$ ls example1.o
example1.o
# Компоновка исходных файлов example1.o и print.a в исполняемый файл с именем example1
$ g++ example1.o print.a -o example1
# Запуск исполяемого файла и отображение результата работы 
$ ./example1 && echo
hello
```

```ShellSession
# Компиляция исходного файла со стандартом С++ 2011 года и создание объектного файла
$ g++ -std=c++11 -I./include -c examples/example2.cpp
# Печать таблицы имен объектного файла
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
# Запуск исполяемого файла (вывод сообщения в файл log.txt)
$ ./example2
# Отображение результата работы 
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
#Создание файла CMakeLists.txt. Указание номера версии cmake.
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
EOF
```

```ShellSession
#Установка стандарта языка С++ и включение требований стандарта
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```

```ShellSession
#Сборка статистической библиотеки print с исходником print.cpp.
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```

```ShellSession
#Добавление директории с хедерами библиотеки print для поиска в них хедеров
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```

```ShellSession
# Создание директории с исполняемым файлом cmake
$ cmake -H. -B_build
...
--Build files have been written to: /home/lol/Ais105/workspace/projects/lab03/_build
# Сборка директории _build с исплняемыми файлами CMake
$ cmake --build _build
Built target print
```

```ShellSession
#Добавление исполняемых файлов в соответствующие исходные проекты
$ cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```

```ShellSession
#Линковка проектов со статистической библиотекой print
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```
Сборка файла _build
```ShellSession
$ cmake --build _build
Built target print
# Сборка директории _build для проекта print
$ cmake --build _build --target print
Built target print
$ cmake --build _build --target example1
Built target example1
$ cmake --build _build --target example2
Built target example2
```

Запуск исполняемых файлов example1 и example2
```ShellSession
# Проверка существования файла libprint.a и вывод информации о нём
$ ls -la _build/libprint.a
-rw-r--r-- 1 lol lol 2498 мар 30 22:08 _build/libprint.a
# Запуск example1 и вывод результата работы на экран
$ _build/example1 && echo
hello
# Запуск исполяемого файла (вывод сообщения в файл log.txt)
$ _build/example2
# Отображение результата работы 
$ cat log.txt && echo
hello
# Удаление результата работы файла example2 (файла log.txt)
$ rm -rf log.txt
```
Работа с файлом CMakeLists.txt из удаленного репозитория
```ShellSession
# Клонирование удаленного репозитория в директорию
$ git clone https://github.com/tp-labs/lab03 tmp
# Перемещение файла CMakeLists.txt в текущий каталог
$ mv -f tmp/CMakeLists.txt .
# Удаление каталога tmp
$ rm -rf tmp
```
Сборка файла CMakeLists.txt
```ShellSession
# Просмотр содержимого файла CMakeLists.txt
$ cat CMakeLists.txt
# Создание директории с исполняемым файлом cmake и указание каталога установки.
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
-- Build files have been written to: /home/lol/Ais105/workspace/projects/lab03/_build
# Сборка файла _build в директорию install
$ cmake --build _build --target install
# Вывод содержимого каталога _install в виде дерева
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
Запись изменений в репозиторий
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
