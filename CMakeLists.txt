# SPDX-License-Identifier: LGPL-3.0-or-later
cmake_minimum_required(VERSION 3.14)
project(brazier-logger VERSION 0.1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

if(MSVC)
    add_compile_options(/utf-8 /bigobj)
endif()

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

file(GLOB_RECURSE BRAZIER_LOGGER_SOURCES CONFIGURE_DEPENDS
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp"
)

if(NOT BRAZIER_LOGGER_SOURCES)
    message(FATAL_ERROR "No sources found in src/")
endif()

add_library(brazier_logger ${BRAZIER_LOGGER_SOURCES})
set_target_properties(brazier_logger PROPERTIES EXPORT_NAME logger)
add_library(brazier::logger ALIAS brazier_logger)

target_include_directories(brazier_logger PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

target_compile_features(brazier_logger PUBLIC cxx_std_20)

if(MSVC)
    set_target_properties(brazier_logger PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS ON)
endif()

install(TARGETS brazier_logger
    EXPORT brazier-loggerTargets
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(EXPORT brazier-loggerTargets
    NAMESPACE brazier::
    DESTINATION ${CMAKE_INSTALL_DATADIR}/brazier-logger
    FILE brazier-loggerTargets.cmake
)

configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/brazier-loggerConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/brazier-loggerConfig.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_DATADIR}/brazier-logger
)

write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/brazier-loggerConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/brazier-loggerConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/brazier-loggerConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_DATADIR}/brazier-logger
)