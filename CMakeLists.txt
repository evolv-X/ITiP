cmake_minimum_required(VERSION 3.20)

project(CmakeExample LANGUAGES CXX)

set(SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp
)

add_executable(${PROJECT_NAME} ${SOURCES})
set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_STANDARD 20)
set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_STANDARD_REQUIRED ON)
if(MSVC)
    target_compile_options(${PROJECT_NAME} PRIVATE /W4 /WX /permissive-)
else()
    target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra -Werror -Wpedantic)
endif()

if(ENABLE_DEBUG)
    if(MSVC)
        target_compile_options(${PROJECT_NAME} PRIVATE /Zi)
    else()
        target_compile_options(${PROJECT_NAME} PRIVATE -g)
    endif()
endif()

find_program(CLANG_TIDY_EXECUTABLE NAMES clang-tidy)
if(CLANG_TIDY_EXECUTABLE)
    set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_CLANG_TIDY "${CLANG_TIDY_EXECUTABLE}")
endif()

find_program(CLANG_FORMAT_EXECUTABLE NAMES clang-format)
if(CLANG_FORMAT_EXECUTABLE)
    add_custom_target(clang-format
        COMMAND "${CLANG_FORMAT_EXECUTABLE}" --dry-run -Werror ${SOURCES}
        VERBATIM
    )
endif()
