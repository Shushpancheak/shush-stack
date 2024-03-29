cmake_minimum_required(VERSION 3.11)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project (
  "shush-stack"
  VERSION 1.0.0
  DESCRIPTION "A safe stack with layers of protection."
)
#set (
#  SOURCES
#  src/${PROJECT_NAME}.cpp
#)
set (
  PUBLIC_HEADERS
  include/${PROJECT_NAME}.hpp
)

# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
# - - - - - - - - - - - - LIBRARY INITIALIZATION- - - - - - - - - - - - - - - -
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

set (
  LIBRARY_NAME
  ${PROJECT_NAME}
)
#set (
#  LIBRARY_TYPE STATIC
#  CACHE STRING "Type of library to build. STATIC or SHARED."
#)

add_library (
  ${LIBRARY_NAME} INTERFACE #${LIBRARY_TYPE}
)

target_include_directories(${LIBRARY_NAME} INTERFACE include/)
#target_include_directories(${LIBRARY_NAME} PUBLIC  include)
#target_include_directories(${LIBRARY_NAME} PRIVATE src)

include(GNUInstallDirs)
install (
  TARGETS                   ${LIBRARY_NAME}
  LIBRARY       DESTINATION ${CMAKE_INSTALL_LIBDIR}
  PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

configure_file(${LIBRARY_NAME}.pc.in ${LIBRARY_NAME}.pc @ONLY)
install (
  FILES       ${CMAKE_BINARY_DIR}/${LIBRARY_NAME}.pc
  DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/pkgconfig
)


set(BUILD_TESTS OFF CACHE BOOL "Build tests")
if (BUILD_TESTS)

# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
# - - - - - - - - - - - - - GTEST INITIALIZATION- - - - - - - - - - - - - - - -
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

add_subdirectory(googletest)
enable_testing()
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
include_directories(${gtest_SOURCE_DIR})

# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
# - - - - - - - - - - - - - - - - - TESTS - - - - - - - - - - - - - - - - - - -
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

set(UNIT_TESTS_NAME "run-tests-${PROJECT_NAME}")
set(UNIT_TESTS_FILE "test/test.cpp")

add_executable(${UNIT_TESTS_NAME} ${UNIT_TESTS_FILE})

# Link test executable against gtest & gtest_main
target_link_libraries(${UNIT_TESTS_NAME} gtest_main ${LIBRARY_NAME})
add_test(${UNIT_TESTS_NAME} ${UNIT_TESTS_NAME})

# For next libraries.
set(BUILD_TESTS OFF CACHE BOOL "Build tests" FORCE)

endif() # BUILD_TESTS

# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
# - - - - - - - - - - - - DEPENDENCIES- - - - - - - - - - - - - - - - - - - - -
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#add_subdirectory(shush-dump)
#add_subdirectory(shush-logs)
#
#include_directories(shush-dump/include)
#include_directories(shush-logs/include)
#
#target_link_libraries(${PROJECT_NAME} INTERFACE shush-dump shush-logs)