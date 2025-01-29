---
author: Moxiaozi
title: CMake 最佳实践总结
categories: ["cmake"]
date: 2025-01-28T15:04:05+08:00
description: "CMake 最佳实践总结"
tags: ["cmake"]
draft: false  # 设置为 false 才会发布
toc: true     # 是否显示目录
math: true    # 是否启用数学公式
---

# CMake 最佳实践总结

本文记录一些 CMake 最佳实践总结，配合示例进行展示。

## 目录
1. [基本原则](#基本原则)
2. [项目结构](#项目结构)
3. [现代 CMake 写法](#现代-cmake-写法)
4. [依赖管理](#依赖管理)
5. [测试集成](#测试集成)
6. [实战示例](#实战示例)

## 基本原则

### 1. 使用现代 CMake
- 始终使用 `target_` 开头的命令而不是全局命令
- 使用 `PRIVATE`、`PUBLIC`、`INTERFACE` 明确依赖关系
- 设置最低 CMake 版本(最好`3.15` 或更高)

### 2. 目标（Target）为中心
- 将每个库和可执行文件视为独立的目标
- 使用目标属性管理编译选项和依赖关系
- 避免使用全局变量和命令

## 项目结构

常见参考项目结构：

```
project_root/
├── CMakeLists.txt
├── cmake/
│   └── modules/
├── include/
│   └── mylib/
├── src/
├── tests/
└── external/
```

项目 CMakeLists.txt 示例：

```cmake
cmake_minimum_required(VERSION 3.15)
project(MyProject VERSION 1.0.0
                  DESCRIPTION "xxx"
                  LANGUAGES CXX)

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# 添加子目录
add_subdirectory(src)
add_subdirectory(tests)
```

## 现代 CMake 写法

### 创建库目标

```cmake
# 创建库
add_library(mylib
    src/feature1.cpp
    src/feature2.cpp
)

# 设置目标属性
target_include_directories(mylib
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

# 设置编译选项
target_compile_options(mylib
    PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>:/W4>
        $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra>
)
```

### 依赖管理

```cmake
# 查找外部依赖
find_package(Boost REQUIRED COMPONENTS system filesystem)

# 链接依赖
target_link_libraries(mylib
    PUBLIC
        Boost::system
    PRIVATE
        Boost::filesystem
)
```

## 依赖管理

### 使用 FetchContent

```cmake
include(FetchContent)

FetchContent_Declare(
    fmt
    GIT_REPOSITORY https://github.com/fmtlib/fmt.git
    GIT_TAG 8.1.1
)

FetchContent_MakeAvailable(fmt)

target_link_libraries(mylib PRIVATE fmt::fmt)
```

## 测试集成

### 启用测试

```cmake
# 在主 CMakeLists.txt 中
enable_testing()

# 在 tests/CMakeLists.txt 中
add_executable(test_mylib
    test_feature1.cpp
    test_feature2.cpp
)

target_link_libraries(test_mylib
    PRIVATE
        mylib
        GTest::gtest_main
)

add_test(NAME test_mylib COMMAND test_mylib)
```

## 示例

下面是一个完整的示例，展示了如何组织一个现代 CMake 项目：

### 主 CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.15)
project(Calculator VERSION 1.0.0
                   DESCRIPTION "简单计算器库"
                   LANGUAGES CXX)

# 基本设置
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# 选项
option(BUILD_TESTS "构建测试" ON)

# 添加库
add_subdirectory(src)

# 测试
if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()
```

### src/CMakeLists.txt

```cmake
add_library(calculator
    calculator.cpp
)

target_include_directories(calculator
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
)

# 安装配置
install(TARGETS calculator
    EXPORT calculatorTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
    INCLUDES DESTINATION include
)

install(DIRECTORY include/
    DESTINATION include
)
```

### tests/CMakeLists.txt

```cmake
find_package(GTest REQUIRED)

add_executable(calculator_test
    calculator_test.cpp
)

target_link_libraries(calculator_test
    PRIVATE
        calculator
        GTest::gtest_main
)

add_test(NAME calculator_test COMMAND calculator_test)
```

### 使用示例

```cpp
// include/calculator/calculator.hpp
namespace calc {
class Calculator {
public:
    double add(double a, double b);
    double subtract(double a, double b);
};
}

// src/calculator.cpp
#include "calculator/calculator.hpp"

namespace calc {
double Calculator::add(double a, double b) {
    return a + b;
}

double Calculator::subtract(double a, double b) {
    return a - b;
}
}

// tests/calculator_test.cpp
#include <gtest/gtest.h>
#include "calculator/calculator.hpp"

TEST(CalculatorTest, Add) {
    calc::Calculator calc;
    EXPECT_DOUBLE_EQ(calc.add(2.0, 3.0), 5.0);
}

TEST(CalculatorTest, Subtract) {
    calc::Calculator calc;
    EXPECT_DOUBLE_EQ(calc.subtract(5.0, 3.0), 2.0);
}
```

## 最佳实践总结

1. **使用现代 CMake 特性**
   - 优先使用 target_ 命令
   - 明确指定依赖关系的可见性
   - 使用生成器表达式处理平台差异

2. **项目组织**
   - 使用清晰的目录结构
   - 将相关文件组织在一起
   - 使用子目录管理复杂项目

3. **依赖管理**
   - 优先使用包管理工具
   - 明确版本要求
   - 使用 find_package 或 FetchContent

4. **可移植性**
   - 使用生成器表达式处理编译器差异
   - 避免硬编码路径
   - 提供配置选项

5. **测试和文档**
   - 集成单元测试
   - 提供清晰的文档
   - 包含使用示例

采用常用最佳实践，可以使得项目将更容易维护、可靠，并且更容易被其他开发者理解和使用，好处多多。