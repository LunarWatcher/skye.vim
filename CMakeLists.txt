cmake_minimum_required(VERSION 3.10)
project(Skye)

if ( ${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR} )
    message( FATAL_ERROR "In-source builds not allowed. Please make a new directory and run CMake from there, or use the -B argument." )
endif()

if (NOT CMAKE_BUILD_TYPE)
    # Prevent explicit flags.
    set(CMAKE_BUILD_TYPE Release)
endif()

set (CMAKE_CXX_STANDARD 20)

if(NOT EXISTS "${CMAKE_BINARY_DIR}/conan.cmake")
  message(STATUS "Downloading conan.cmake from https://github.com/conan-io/cmake-conan")
  file(DOWNLOAD "https://raw.githubusercontent.com/conan-io/cmake-conan/v0.16.1/conan.cmake"
                "${CMAKE_BINARY_DIR}/conan.cmake"
                TLS_VERIFY ON)
endif()

include(${CMAKE_BINARY_DIR}/conan.cmake)

conan_cmake_configure (REQUIRES nlohmann_json/3.9.1 cpr/1.6.0
                        GENERATORS cmake)
conan_cmake_autodetect (settings)
conan_cmake_install (PATH_OR_REFERENCE . BUILD missing REMOTE conan-center SETTINGS ${settings})

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

if (NOT WIN32)
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pedantic -Wall -Wextra -Wno-c++11-narrowing")
    set (CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g -O0 -fsanitize=undefined")
    set (CMAKE_CXX_FLAGS_RELEASE "-O3")
else()
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
    set (CMAKE_CXX_FLAGS_DEBUG "/Zi")
endif()

add_subdirectory(src)
