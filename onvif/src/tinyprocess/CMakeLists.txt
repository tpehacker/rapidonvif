cmake_minimum_required(VERSION 2.8)

project(tiny-process-library)

if(MSVC)
  add_definitions(-D_CRT_SECURE_NO_WARNINGS)
else()
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wextra -Wno-unused-parameter")
endif()

find_package(Threads REQUIRED)

set(process_source_files process.cpp)

if(WIN32)
  list(APPEND process_source_files process_win.cpp)
  #If compiled using MSYS2, use sh to run commands
  if(MSYS)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DMSYS_PROCESS_USE_SH")
  endif()
else()
  list(APPEND process_source_files process_unix.cpp)
endif()

add_library(tiny-process-library ${process_source_files})
add_executable(examples examples.cpp)

target_link_libraries(tiny-process-library ${CMAKE_THREAD_LIBS_INIT})
target_link_libraries(examples tiny-process-library)

# To enable tests: cmake -DENABLE_TESTING=1 .
if(ENABLE_TESTING)
  enable_testing()
  add_subdirectory(tests)
endif()
