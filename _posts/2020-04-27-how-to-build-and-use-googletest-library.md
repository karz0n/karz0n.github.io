---
title: "How to build and use GoogleTest library"
date: 2020-04-27
categories: [Development,Bootstrap]
tags: [gtest,gmock]
---

# Clone
Open terminal and run this command:
```bash
$ git clone git@github.com:google/googletest.git
```

# Build
Open terminal from source directory and run these commands:
```bash
$ cd googletest
$ cmake -S . -B build \
-DCMAKE_CXX_STANDARD=17 \
-DCMAKE_INSTALL_PREFIX=~/opt/googletest
$ cmake --build build --parallel --target install
```
After you finish, compiled `GoogleTest` library can be found in `~/opt/googletest` dir.

# Use

Create file `GoogleTest.cmake` with content below and include it by `include(...)` command:
```cmake
list(APPEND CMAKE_PREFIX_PATH "~/opt/googletest")

find_package(GTest CONFIG REQUIRED)

if (TARGET GTest::gtest)
    get_property(GTEST_LOCATION
        TARGET
            GTest::gtest
        PROPERTY
            LOCATION)
    message(STATUS "Found GTest: ${GTEST_LOCATION}")
else()
    message(FATAL_ERROR "GTest library NOT found")
endif()

if (TARGET GTest::gmock)
    get_property(GMOCK_LOCATION
        TARGET
            GTest::gmock
        PROPERTY
            LOCATION)
    message(STATUS "Found GMock: ${GMOCK_LOCATION}")
else()
    message(FATAL_ERROR "GMock library NOT found")
endif()
```
Pay attention, dir `~/opt/googletest` is used to find `GoogleTest` library.

After that you can link cmake target with `GoogleTest` library:
```cmake
target_link_libraries(<TARGET_NAME>
    PRIVATE
        GTest::gtest
        GTest::gtest_main
        GTest::gmock
        GTest::gmock_main
    )
```
Of cource, linking with `gtest` and `gmock` at the same time isn't necessary, thus `GTest::gmock` and `GTest::gmock_main` can be removed.

# Use (another way)

Sometimes automatic fetch `GoogleTest` source and build it can be more conveniently. In this case create file `GoogleTest.cmake` with content below and include it by `include(...)` command:
```cmake
include(FetchContent)

set(_NAME googletest)
set(_TAG master)
set(_URL https://github.com/google/googletest)

FetchContent_Declare(
  ${_NAME}
  GIT_REPOSITORY ${_URL}
  GIT_TAG ${_TAG}
)

FetchContent_GetProperties(${_NAME})
if(${_NAME}_POPULATED)
    message(STATUS "${_NAME} has already populated")
    return()
endif()
FetchContent_Populate(${_NAME})

set(_SOURCE_DIR ${${_NAME}_SOURCE_DIR})
set(_BINARY_DIR ${${_NAME}_BINARY_DIR})

#
# Configure and compile
#
execute_process(COMMAND "${CMAKE_COMMAND}" -G "${CMAKE_GENERATOR}" ${_SOURCE_DIR}
    WORKING_DIRECTORY "${_BINARY_DIR}" )
execute_process(COMMAND "${CMAKE_COMMAND}" --build . --parallel
    WORKING_DIRECTORY "${_BINARY_DIR}" )

find_package(Threads REQUIRED)

#
# Define GTest library target
#
add_library(${_NAME}-gtest INTERFACE IMPORTED GLOBAL)
target_link_libraries(${_NAME}-gtest
    INTERFACE
        ${_BINARY_DIR}/lib/${CMAKE_STATIC_LIBRARY_PREFIX}gtest${CMAKE_STATIC_LIBRARY_SUFFIX}
        ${_BINARY_DIR}/lib/${CMAKE_STATIC_LIBRARY_PREFIX}gtest_main${CMAKE_STATIC_LIBRARY_SUFFIX}
    )
target_include_directories(${_NAME}-gtest
    INTERFACE ${_SOURCE_DIR}/googletest/include)
set_property(TARGET ${_NAME}-gtest
             APPEND
             PROPERTY INTERFACE_LINK_LIBRARIES ${CMAKE_THREAD_LIBS_INIT})

#
# Define GMock library target
#
add_library(${_NAME}-gmock INTERFACE IMPORTED GLOBAL)
target_link_libraries(${_NAME}-gmock
    INTERFACE
        ${_BINARY_DIR}/lib/${CMAKE_STATIC_LIBRARY_PREFIX}gmock${CMAKE_STATIC_LIBRARY_SUFFIX}
        ${_BINARY_DIR}/lib/${CMAKE_STATIC_LIBRARY_PREFIX}gmock_main${CMAKE_STATIC_LIBRARY_SUFFIX}
    )
target_include_directories(${_NAME}-gmock
    INTERFACE ${_SOURCE_DIR}/googlemock/include)
set_property(TARGET ${_NAME}-gmock
             APPEND
             PROPERTY INTERFACE_LINK_LIBRARIES ${CMAKE_THREAD_LIBS_INIT})
```


