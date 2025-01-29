---
author: Moxiaozi
title: CMake 常用变量与函数参考指南
categories: ["cmake"]
date: 2025-01-28T15:04:05+08:00
description: "CMake 常用变量与函数参考指南"
tags: ["cmake"]
draft: false  # 设置为 false 才会发布
toc: true     # 是否显示目录
math: true    # 是否启用数学公式
---

# CMake 常用变量与函数参考指南

## 目录
1. [常用变量](#常用变量)
   - [项目信息变量](#项目信息变量)
   - [系统信息变量](#系统信息变量)
   - [构建配置变量](#构建配置变量)
   - [路径相关变量](#路径相关变量)
   - [编译器和工具链变量](#编译器和工具链变量)
2. [常用函数和命令](#常用函数和命令)
   - [项目配置命令](#项目配置命令)
   - [目标管理命令](#目标管理命令)
   - [属性设置命令](#属性设置命令)
   - [依赖处理命令](#依赖处理命令)
   - [安装和打包命令](#安装和打包命令)
3. [实用示例](#实用示例)
4. [参考链接](#参考链接)

## 常用变量

### 项目信息变量

```cmake
# 项目名称
${PROJECT_NAME}                # 当前项目名称
${CMAKE_PROJECT_NAME}          # 顶层项目名称

# 项目版本
${PROJECT_VERSION}            # 完整版本号 (例如 1.2.3)
${PROJECT_VERSION_MAJOR}      # 主版本号 (1)
${PROJECT_VERSION_MINOR}      # 次版本号 (2)
${PROJECT_VERSION_PATCH}      # 补丁版本号 (3)

# 项目目录
${PROJECT_SOURCE_DIR}        # 项目源代码根目录
${PROJECT_BINARY_DIR}        # 项目构建目录
```

示例：
```cmake
project(MyApp VERSION 1.2.3)
message("Project name: ${PROJECT_NAME}")
message("Project version: ${PROJECT_VERSION}")
message("Source directory: ${PROJECT_SOURCE_DIR}")
```

### 系统信息变量

```cmake
# 操作系统
${CMAKE_SYSTEM_NAME}         # 操作系统名称 (Linux, Windows, Darwin)
${CMAKE_SYSTEM_VERSION}      # 操作系统版本
${CMAKE_SYSTEM_PROCESSOR}    # 处理器架构

# 平台特定
${WIN32}                     # Windows 平台为 TRUE
${UNIX}                      # UNIX-like 平台为 TRUE
${APPLE}                     # macOS 平台为 TRUE
${MSVC}                      # Microsoft Visual C++ 编译器为 TRUE
```

示例：
```cmake
if(WIN32)
    message("Configuring for Windows")
    target_compile_definitions(myapp PRIVATE WIN32_LEAN_AND_MEAN)
elseif(UNIX AND NOT APPLE)
    message("Configuring for Linux")
    target_compile_definitions(myapp PRIVATE _GNU_SOURCE)
endif()
```

### 构建配置变量

```cmake
# 构建类型
${CMAKE_BUILD_TYPE}          # Debug, Release, RelWithDebInfo, MinSizeRel
${CMAKE_DEBUG_POSTFIX}       # Debug 构建的后缀

# 输出目录
${CMAKE_RUNTIME_OUTPUT_DIRECTORY}  # 可执行文件输出目录
${CMAKE_LIBRARY_OUTPUT_DIRECTORY}  # 库文件输出目录
${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}  # 静态库输出目录
```

示例：
```cmake
# 设置构建类型
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# 设置输出目录
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
```

### 路径相关变量

```cmake
${CMAKE_CURRENT_SOURCE_DIR}  # 当前处理的 CMakeLists.txt 所在目录
${CMAKE_CURRENT_BINARY_DIR}  # 当前目标的构建目录
${CMAKE_MODULE_PATH}         # CMake 模块查找路径
${CMAKE_PREFIX_PATH}         # 第三方库查找路径
```

示例：
```cmake
# 添加自定义模块路径
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

# 设置第三方库查找路径
list(APPEND CMAKE_PREFIX_PATH "/usr/local/lib/custom_lib")
```

### 编译器和工具链变量

```cmake
# 编译器标识符
${CMAKE_CXX_COMPILER_ID}    # 编译器标识 (GNU, Clang, MSVC, etc.)
${CMAKE_CXX_COMPILER}       # C++ 编译器路径
${CMAKE_C_COMPILER}         # C 编译器路径

# 编译选项
${CMAKE_CXX_STANDARD}       # C++ 标准版本
${CMAKE_CXX_FLAGS}          # C++ 编译标志
${CMAKE_CXX_FLAGS_DEBUG}    # Debug 模式的 C++ 编译标志
${CMAKE_CXX_FLAGS_RELEASE}  # Release 模式的 C++ 编译标志
```

示例：
```cmake
# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 根据编译器添加编译选项
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(myapp PRIVATE -Wall -Wextra)
elseif(MSVC)
    target_compile_options(myapp PRIVATE /W4)
endif()
```

## 常用函数和命令

### 项目配置命令

```cmake
# 项目声明
cmake_minimum_required(VERSION 3.15)
project(MyProject 
    VERSION 1.0.0
    DESCRIPTION "Project description"
    LANGUAGES CXX
)

# 选项设置
option(BUILD_TESTS "Build test suite" ON)
set(MY_OPTION "value" CACHE STRING "Option description")
```

### 目标管理命令

```cmake
# 添加目标
add_executable(myapp src/main.cpp)            # 可执行文件
add_library(mylib STATIC src/lib.cpp)         # 静态库
add_library(mylib SHARED src/lib.cpp)         # 动态库
add_library(mylib INTERFACE)                  # 接口库

# 目标属性
target_include_directories(mylib
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

target_compile_definitions(mylib
    PUBLIC
        MY_PUBLIC_DEFINE
    PRIVATE
        MY_PRIVATE_DEFINE
)

target_compile_options(mylib
    PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>:/W4>
        $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall>
)
```

### 依赖处理命令

```cmake
# 查找包
find_package(Boost 1.70 REQUIRED COMPONENTS system filesystem)
find_library(MATH_LIB m)

# 链接依赖
target_link_libraries(myapp
    PRIVATE
        mylib
        Boost::system
        Boost::filesystem
)

# 使用 FetchContent
include(FetchContent)
FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG release-1.12.1
)
FetchContent_MakeAvailable(googletest)
```

### 安装和打包命令

```cmake
# 安装规则
install(TARGETS myapp mylib
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
)

install(DIRECTORY include/
    DESTINATION include
)

# CPack 配置
include(CPack)
set(CPACK_PACKAGE_NAME "${PROJECT_NAME}")
set(CPACK_PACKAGE_VERSION "${PROJECT_VERSION}")
```

## 实用示例

### 1. 配置文件生成

```cmake
# 配置头文件
configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/config.h.in
    ${CMAKE_CURRENT_BINARY_DIR}/config.h
)

# config.h.in 内容
#define PROJECT_NAME "@PROJECT_NAME@"
#define PROJECT_VERSION "@PROJECT_VERSION@"
#define PROJECT_VERSION_MAJOR @PROJECT_VERSION_MAJOR@
```

### 2. 条件编译

```cmake
if(MSVC)
    target_compile_definitions(myapp PRIVATE
        _CRT_SECURE_NO_WARNINGS
        NOMINMAX
        WIN32_LEAN_AND_MEAN
    )
endif()

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_definitions(myapp PRIVATE DEBUG_MODE)
endif()
```

### 3. 自定义命令

```cmake
add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/generated.cpp
    COMMAND python ${CMAKE_CURRENT_SOURCE_DIR}/generate.py
    DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/template.txt
    COMMENT "Generating source file"
)

add_custom_target(generate_sources
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/generated.cpp
)
```

### 4. 测试配置

```cmake
enable_testing()

add_executable(test_suite tests/test_main.cpp)
target_link_libraries(test_suite PRIVATE
    GTest::gtest_main
    mylib
)

add_test(NAME test_suite COMMAND test_suite)
```

## 参考链接

1. CMake 官方文档：
   - [CMake Documentation](https://cmake.org/documentation/)
   - [CMake Reference Documentation](https://cmake.org/cmake/help/latest/)
   - [CMake Tutorial](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)

2. CMake 最佳实践：
   - [Modern CMake](https://cliutils.gitlab.io/modern-cmake/)
   - [Effective Modern CMake](https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1)
   - [Professional CMake](https://crascit.com/professional-cmake/)

3. CMake 工具和扩展：
   - [CMake Tools for VS Code](https://github.com/microsoft/vscode-cmake-tools)
   - [CMake Language Support for VS Code](https://github.com/twxs/vs.language.cmake)
   - [CMake Generator Expressions](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html)

4. CMake 社区资源：
   - [CMake Community Wiki](https://gitlab.kitware.com/cmake/community/-/wikis/home)
   - [Awesome CMake](https://github.com/onqtam/awesome-cmake)
   - [More Modern CMake](https://hsf-training.github.io/hsf-training-cmake-webpage/)