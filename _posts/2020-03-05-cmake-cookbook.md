---
title: "My cmake cookbook"
date: 2020-03-05
categories: [Development,CMake]
tags: [cmake,cpp11]
---

## [R01] - Activate C++11 standard in cmake
**Project** specific configuration of C++ standard, works for all targets that will be defined.
```cmake
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
```
**Target** specific configration:
```cmake
set_target_properties(myTarget PROPERTIES
    CXX_STANDARD 11
    CXX_STANDARD_REQUIRED YES
    CXX_EXTENSIONS NO
)
```
Also there is additional approach to enable only specific feature by `target_compile_features()`:
```cmake
target_compile_features(myTarget
    PUBLIC
        cxx_variadic_templates
        cxx_nullptr
    PRIVATE
        cxx_lambdas
)
```

## [R02] - Add project version

```cmake
# Specify project version number.
set (App_VERSION_MAJOR 1)
set (App_VERSION_MINOR 0)

# ...
project (App VERSION "${App_VERSION_MAJOR}.${App_VERSION_MINOR}")
# ...

# ...
configure_file (
  "${PROJECT_SOURCE_DIR}/AppConfig.h.in"
  "${PROJECT_BINARY_DIR}/AppConfig.h"
  )
# ...

include_directories("${PROJECT_BINARY_DIR}")
```

Config file template:
```
#define App_VERSION_MAJOR @App_VERSION_MAJOR@
#define App_VERSION_MINOR @App_VERSION_MINOR@
```

Using in source code:
```cpp
#include <iostream>

#include "AppConfig.h"

int main (int argc, char *argv[])
{
  std::cout << "Major: " << App_VERSION_MAJOR
            << "Minor: " << App_VERSION_MINOR
            << std::endl;
  return 0;
}
```

## [R03] - Define macro variable

```cmake
# ...
add_definitions(-DSOME_IMPORTANT_DEFINITION)
# ...
```

Using of macro variable in source code:
```cpp
#ifdef SOME_IMPORTANT_DEFINITION
# ... some staff ...
#endif
```
