[![CI](https://github.com/Policaika/lab04/actions/workflows/ci.yml/badge.svg)](https://github.com/Policaika/lab04/actions/workflows/ci.yml)

# Лабораторная работа №4

## Задание 1

Вы продолжаете проходить стажировку в "Formatter Inc." (см подробности).

В прошлый раз ваше задание заключалось в настройке автоматизированной системы CMake.

Сейчас вам требуется настроить систему непрерывной интеграции для библиотек и приложений, с которыми вы работали в прошлый раз. Настройте сборочные процедуры на различных платформах:

используйте GitHub Actions для сборки на операционной системе Linux с использованием компиляторов gcc и clang;
используйте AppVeyor для сборки на операционной системе Windows. (Более не актуален, поэтому не используем).

### YML.CI:

```YAML

name: CI

on:
  push:
    branches: [ master, main ]

jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        include:
          - os: ubuntu-latest
            compiler: clang
            cc: clang
            cxx: clang++
          - os: ubuntu-latest
            compiler: gcc
            cc: gcc
            cxx: g++
          - os: windows-latest
            compiler: msvc
            cc: cl
            cxx: cl
          - os: windows-latest
            compiler: clang
            cc: clang
            cxx: clang++

    steps:
      - name: Pull repo
        uses: actions/checkout@v4

      - name: Setup compiler
        if: runner.os == 'Linux' && matrix.compiler == 'clang'
        run: sudo apt-get update && sudo apt-get install -y clang

      - name: Setup compiler
        if: runner.os == 'Windows' && matrix.compiler == 'clang'
        uses: egor-tensin/setup-clang@v1

      - name: Cmake build
        env:
          CC: ${{ matrix.cc }}
          CXX: ${{ matrix.cxx }}
        run: cmake -S . -B _build -DCMAKE_INSTALL_PREFIX=_install

      - name: Build project
        run: cmake --build _build --config Release

      - name: Install target
        run: cmake --install _build --prefix _install

```

### Main CMakeLists.txt:


```CMake
cmake_minimum_required(VERSION 3.10)

project(lab03)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex_lib)
add_subdirectory(solver_lib)
add_subdirectory(hello_world_application)
add_subdirectory(solver_application)

install(TARGETS formatter formatter_ex_lib solver_lib solver hello_world
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(FILES formatter_lib/formatter.h solver_lib/solver.h
        DESTINATION include)
```
