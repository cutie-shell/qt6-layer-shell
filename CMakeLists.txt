# SPDX-FileCopyrightText: 2021 Aleix Pol Gonzalez <aleixpol@kde.org>
# SPDX-License-Identifier: BSD-3-Clause

cmake_minimum_required(VERSION 3.16)

project(layershellqt6)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(PROJECT_VERSION "6.27.80")
set(PROJECT_VERSION_MAJOR 6)

set(CMAKE_C_STANDARD 99)

set(QT_MIN_VERSION "6.4.0")
set(KF6_MIN_VERSION "5.103.0")
set(KDE_COMPILERSETTINGS_LEVEL "5.82")

set(QT_MAJOR_VERSION "6")

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(ECM ${KF6_MIN_VERSION} REQUIRED NO_MODULE)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake ${ECM_MODULE_PATH})

include( GNUInstallDirs )
include(ECMSetupVersion)
include(ECMDeprecationSettings)
include(ECMGenerateHeaders)
include(CMakePackageConfigHelpers)
include(FeatureSummary)
include(GenerateExportHeader)
include(KDEClangFormat)
include(ECMQtDeclareLoggingCategory)


find_package(Qt${QT_MAJOR_VERSION} ${QT_MIN_VERSION} CONFIG REQUIRED COMPONENTS WaylandClient Qml)
if (QT_MAJOR_VERSION EQUAL "5")
    find_package(Qt5XkbCommonSupport REQUIRED PRIVATE)
    find_package(QtWaylandScanner REQUIRED)
endif()
find_package(WaylandScanner REQUIRED)
find_package(Wayland 1.3 COMPONENTS Client Server)
find_package(WaylandProtocols REQUIRED)
find_package(PkgConfig REQUIRED)

pkg_check_modules(XKBCOMMON xkbcommon REQUIRED IMPORTED_TARGET)

set_package_properties(Wayland PROPERTIES
                       TYPE REQUIRED)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu90")

ecm_setup_version(${PROJECT_VERSION} VARIABLE_PREFIX LAYERSHELLQT
                        VERSION_HEADER "${CMAKE_CURRENT_BINARY_DIR}/layershellqt_version.h"
                        PACKAGE_VERSION_FILE "${CMAKE_CURRENT_BINARY_DIR}/LayerShellQt6ConfigVersion.cmake"
                        SOVERSION 6)

file(GLOB_RECURSE ALL_CLANG_FORMAT_SOURCE_FILES *.cpp *.h)
kde_clang_format(${ALL_CLANG_FORMAT_SOURCE_FILES})

ecm_set_disabled_deprecation_versions(QT 5.15.2
    KF 5.101
)

add_subdirectory(src)
#add_subdirectory(tests)

set(CMAKECONFIG_INSTALL_DIR ${CMAKE_INSTALL_LIBDIR}/LayerShellQt6)
install(EXPORT LayerShellQtTargets
        NAMESPACE LayerShellQt6::
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/LayerShellQt6
        FILE LayerShellQt6Targets.cmake)

configure_package_config_file(LayerShellQtConfig.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/LayerShellQt6Config.cmake"
    INSTALL_DESTINATION ${CMAKECONFIG_INSTALL_DIR})

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/LayerShellQt6Config.cmake
              ${CMAKE_CURRENT_BINARY_DIR}/LayerShellQt6ConfigVersion.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/LayerShellQt6)

feature_summary(WHAT ALL INCLUDE_QUIET_PACKAGES FATAL_ON_MISSING_REQUIRED_PACKAGES)
