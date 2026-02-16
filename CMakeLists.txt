cmake_minimum_required(VERSION 3.5)
project(rrpigpio LANGUAGES C VERSION 0.71)

list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/cmake)

find_package(Threads REQUIRED)
find_package(RT REQUIRED)

option(BUILD_SHARED_LIBS "Create shared libraries" ON)

add_compile_options(-Wall)

# librrpigpio.(so|a)
add_library(rrpigpio pigpio.c command.c custom.cext)
target_link_libraries(rrpigpio RT::RT Threads::Threads)

# Configure and install project
include(GenerateExportHeader)
include(CMakePackageConfigHelpers)

generate_export_header(rrpigpio)

install(TARGETS rrpigpio
    EXPORT rrpigpioTargets
    LIBRARY DESTINATION lib${LIB_SUFFIX}
    ARCHIVE DESTINATION lib${LIB_SUFFIX}
    INCLUDES DESTINATION include
)

write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/rrpigpioConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

export(EXPORT rrpigpioTargets
    FILE "${CMAKE_CURRENT_BINARY_DIR}/rrpigpioTargets.cmake"
    NAMESPACE rrpigpio::
)

set(ConfigPackageLocation lib/cmake/rrpigpio)

install(EXPORT rrpigpioTargets
    FILE rrpigpioTargets.cmake
    NAMESPACE rrpigpio::
    DESTINATION ${ConfigPackageLocation}
)

install(
    FILES
        ${CMAKE_CURRENT_LIST_DIR}/cmake/rrpigpioConfig.cmake
		${CMAKE_CURRENT_LIST_DIR}/cmake/FindRT.cmake
        "${CMAKE_CURRENT_BINARY_DIR}/rrpigpioConfigVersion.cmake"
    DESTINATION
        ${ConfigPackageLocation}
)

install(FILES pigpio.h
    DESTINATION include
    PERMISSIONS OWNER_READ OWNER_WRITE GROUP_READ WORLD_READ
)

# package project
set(CPACK_PACKAGE_NAME "rrpigpio")
set(CPACK_PACKAGE_VERSION "${PROJECT_VERSION}")
set(CPACK_PACKAGE_ARCHITECTURE "${CMAKE_SYSTEM_PROCESSOR}")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Aaron Spiteri <azzmosphere@gmail.com>")
set(CPACK_DEBIAN_PACKAGE_DEPENDS "libc6")
set(CPACK_DEBIAN_PACKAGE_SECTION "libs")
set(CPACK_DEBIAN_PACKAGE_DESCRIPTION "pigpio library (Ryder Robots fork)
 Unofficial fork of pigpio with BCM2711 fixes.
 Based on pigpio by Joan (joan2937).
 NOT supported by the original author.")
set(CPACK_GENERATOR "DEB")

include(CPack)