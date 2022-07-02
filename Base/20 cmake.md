# 20 cmake
#do/start  https://www.youtube.com/watch?v=LZwEtbc9gEA

# Минимальный проект
Все CMake проект начинаются с файла `CMakeLists.txt` - это что-то на подобие проектного файла, определяющий все что связано со сборкой.

```cmake
cmake_minimum_required(VERSION 3.15)

set(projectname MyApp)
project(${projectname})

set(SOURCES main.cpp test.cpp)

set(HEADERS test.h)

add_executable(${projectname} ${SOURCES} ${HEADERS})

set_target_properties(
		${projectname} PROPERTIES
		CXX_STANDART 11
		CXX_STANDART_REQUIRED ON
)
```

1. `cmake_minimum_required()` - устанавливаем минимальную версию cmake, при наличии более старой версии выдаст ошибку.
2. project - создание проекта с именем `MyApp` - отвечает не только за имя проекта, но и за инициализацию чего-то, в общем обязательная функция, как и версия `cmake_minimum_required()`.
3. `add_executable` - из 2 объектных файлов хотим собрать исполняемый файл `myExe`, файла .h не обязательно добавлять, т.к. компилятор итак при [[Сборка проектов на C++#Препроцессинг|препроцесинге]] их вставляет в `.cpp`, но так они хотя бы отображаются в проекте.
4. set_target_properties - указываем свойства проекта - собираем проект на как минимум 11 стандарте

Для сборки следует выполнить следующее

```cmake
mkdir build && cd build
cmake .. (если cmakelists находится на каталог выше, либо . если в папке build)
cmake --build .
cmake --build . --config Release --target MyApp
Если написать cmake .. -G "MinGW Makefiles" то проект будет собираться не с дефолтными инструментами а с MinGW, допустим на windows по дефолту проект собирается для Visual Studio
```

Имена команд не чувствительны к регистру

## Команда project()
В LANGUAGES указываются языки C, CXX, Fortran, ASM, Java и другие(по умолчанию C и CXX). Можно указать `LANGUAGES NONE`, что означает поддержку ни одного из языков.

```cmake
project(myProh C CXX)
```

Команда `project()` проверяет наличие компилятора для каждого `LANGUAGES` и их работоспособность. Проблемы с настройкой компилятора и компоновщика обнаруживаются очень рано. Как только эти проверки пройдены, CMake устанавливает ряд переменных и свойств, которые управляют сборкой для включенных языков. 

Если `CMakeLists.txt` не вызывает project() или не вызывает его достаточно рано, CMake будет неявно вызывать его внутренне для языков по умолчанию `C` и `CXX`, чтобы гарантировать, что компиляторы и компоновщики правильно настроены для других команд, которые полагаются на них.

Когда проверки компилятора и компоновщика, выполняемые CMake, успешны, их результаты кэшируются, так что их не нужно повторять в последующих запусках CMake. Эти кэшированные сведения хранятся в каталоге сборки в `CMakeCache.txt`.

## Команда add_executable()
Эта команда говорит CMake'у создать исполняемый файл из набора исходных файлов. Базовая запись команды
```cmake
add_executable(targetName source1 [source2 ...])
```
`targetName` - это имя исполняемого файла. По умолчанию на Windows имя исполняемого файла будет таким `targetName.exe`, на Unix(Linux, MacOS и т.л.) - `targetName`. 

Несколько исполняемых файлов могут быть определены в пределах одного `CMakeLists.txt`, вызывая `add_executable()` несколько раз с разными целевыми именами. Если одно и то же целевое имя используется более чем в одной команде `add_executable()`, будет ошибка.

## Команда add_library
Компилирует библиотеку, по умолчанию Static

```cmake
add_library(targetName [STATIC | SHARED | MODULE]
						[EXCLUDE_FROM_ALL]
						source1 [source2 ...]
)
```

Если не указывать в CMakeLists.txt, то можно во время запуска cmake

```shell
cmake -DBUILD_SHARED_LIBS=YES ..
cmake --build .
```

## Команда target_link_libraries
Связывает библиотеки между собой, либо с исполняемым файлом

```cmake
cmake_minimum_required(VERSION 3.15)

set(project_name MyApp)
project(${project_name})


add_executable(${project_name} main.cpp)
add_library(test test.cpp)
target_link_libraries(${project_name} test)

set_target_properties(
		${projectname} PROPERTIES
		CXX_STANDART 11
		CXX_STANDART_REQUIRED ON
)
```