﻿cmake_minimum_required(VERSION 3.14)

project(ncmdump LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_POLICY_DEFAULT_CMP0077 NEW)
set(WITH_ZLIB OFF)
set(BUILD_TESTING OFF)
add_subdirectory(taglib)

if(MSVC)
  add_compile_options("$<$<CXX_COMPILER_ID:MSVC>:/execution-charset:utf-8>")
endif()

FILE(GLOB HEADERS ./*.h)
FILE(GLOB COMMON_SOURCES cJSON.cpp aes.cpp main.cpp ncmcrypt.cpp)
FILE(GLOB WIN_SOURCES win32_init.cpp)

if(WIN32)
  set(SOURCES ${COMMON_SOURCES} ${WIN_SOURCES})
else()
  set(SOURCES ${COMMON_SOURCES})
endif()
add_executable(ncmdump
  ${HEADERS}
  ${SOURCES}
)
target_link_libraries(ncmdump tag)
target_include_directories(ncmdump PRIVATE taglib)
target_include_directories(ncmdump PRIVATE taglib/taglib)
target_include_directories(ncmdump PRIVATE taglib/taglib/toolkit)
target_include_directories(ncmdump PRIVATE taglib/taglib/mpeg/id3v2)

if(WIN32)
  if(CMAKE_COMPILER_IS_GNUCXX)
    target_link_options(ncmdump PRIVATE -static)
  endif()
endif()

include(GNUInstallDirs)
install(TARGETS ncmdump
  BUNDLE DESTINATION .
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
