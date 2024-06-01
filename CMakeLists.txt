cmake_minimum_required(VERSION 3.25) 
project(allocator)

include(FetchContent)
FetchContent_Declare(
  googletest
)

add_library(allocator_lib INTERFACE)
set_target_properties(allocator_lib PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON
)
target_include_directories(allocator_lib INTERFACE "${CMAKE_CURRENT_SOURCE_DIR}")


add_executable(allocator main.cpp)

if (CMAKE_CXX_COMPILER_ID MATCHES "Clang" OR
    CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    target_compile_options(containers PRIVATE 
        -g
        -Wall
        -Wextra
        -Werror
        -pedantic
        -fsanitize=leak
    )
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    target_compile_options(containers PRIVATE
        /EHsc
        /W4
        /WX
    )
endif()
target_link_libraries(allocator PRIVATE allocator_lib)

install(TARGETS allocator DESTINATION bin)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/../cmake")
include(package)
add_package("allocator" "01")
include(testing)
run_tests(allocator_lib)
