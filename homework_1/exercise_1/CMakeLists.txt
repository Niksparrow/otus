cmake_minimum_required(VERSION 3.17)

if(DEFINED $ENV{GITHUB_RUN_NUMBER})
    project(HelloWorld VERSION 0.0.$ENV{GITHUB_RUN_NUMBER})
    set(PROJECT_VERSION_PATCH $ENV{GITHUB_RUN_NUMBER})
else()
    project(HelloWorld)
    set(PROJECT_VERSION_PATCH 1)
endif()

configure_file(${PROJECT_SOURCE_DIR}/version.h.in ${PROJECT_SOURCE_DIR}/version.h)

add_executable(HelloWorld main.cpp)
add_library(HelloWorld_lib lib.cpp)

set_target_properties(HelloWorld HelloWorld_lib PROPERTIES
        CXX_STANDARD 14
        CXX_STANDARD_REQUIRED ON
        )

target_include_directories(HelloWorld PRIVATE "${CMAKE_BINARY_DIR}")

target_link_libraries(HelloWorld PRIVATE HelloWorld_lib)

if (MSVC)
    target_compile_options(HelloWorld PRIVATE /W4)
    target_compile_options(HelloWorld_lib PRIVATE /W4)
    #target_compile_options(test_version PRIVATE /W4)
else ()
    target_compile_options(HelloWorld PRIVATE
            -Wall -Wextra -pedantic -Werror
            )
    target_compile_options(HelloWorld_lib PRIVATE
            -Wall -Wextra -pedantic -Werror
            )
    #target_compile_options(test_version PRIVATE
     #       -Wall -Wextra -pedantic -Werror
     #       )
endif()

install(TARGETS HelloWorld RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)
set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

include(CPack)


