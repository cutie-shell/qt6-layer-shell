# SPDX-FileCopyrightText: 2021 Aleix Pol Gonzalez <aleixpol@kde.org>
# SPDX-License-Identifier: BSD-3-Clause

remove_definitions(-DQT_NO_SIGNALS_SLOTS_KEYWORDS)

add_library(LayerShellQtInterface6 SHARED)
qt6_generate_wayland_protocol_client_sources(LayerShellQtInterface6 FILES
    ${WaylandProtocols_DATADIR}/stable/xdg-shell/xdg-shell.xml
    ${WaylandProtocols_DATADIR}/staging/xdg-activation/xdg-activation-v1.xml
    ${CMAKE_CURRENT_SOURCE_DIR}/wlr-layer-shell-unstable-v1.xml
)

ecm_qt_declare_logging_category(LayerShellQtInterface6
    HEADER
        layershellqt_logging.h
    IDENTIFIER
        LAYERSHELLQT
    CATEGORY_NAME
        layershellqt
)

target_sources(LayerShellQtInterface6 PRIVATE
    qwaylandxdgactivationv1.cpp
    qwaylandlayersurface.cpp
    qwaylandlayershellintegration.cpp
    interfaces/window.cpp
    interfaces/shell.cpp
)

target_link_libraries(LayerShellQtInterface6 PUBLIC Qt::Gui)
target_link_libraries(LayerShellQtInterface6 PRIVATE Qt::WaylandClientPrivate Wayland::Client PkgConfig::XKBCOMMON)
if (TARGET Qt::XkbCommonSupportPrivate)
    target_link_libraries(LayerShellQtInterface6 PRIVATE Qt::XkbCommonSupportPrivate)
endif()
target_include_directories(LayerShellQtInterface6 PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/LayerShellQt>"
                                                            "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>"
                                              INTERFACE "$<INSTALL_INTERFACE:${KDE_INSTALL_INCLUDEDIR}/>"
)

set_target_properties(LayerShellQtInterface6 PROPERTIES VERSION   ${LAYERSHELLQT_VERSION}
                                                       SOVERSION ${LAYERSHELLQT_SOVERSION}
                                                       EXPORT_NAME Interface
)

add_library(layer-shell6 SHARED qwaylandlayershellintegrationplugin.cpp)
target_link_libraries(layer-shell6 LayerShellQtInterface6 Qt::WaylandClient Qt::WaylandClient Qt::WaylandClientPrivate Wayland::Client PkgConfig::XKBCOMMON)
if (TARGET Qt::XkbCommonSupportPrivate)
    target_link_libraries(layer-shell6 Qt::XkbCommonSupportPrivate)
endif()

ecm_generate_headers(LayerShellQt_HEADERS
    HEADER_NAMES
        Shell
        Window
    RELATIVE interfaces/
    REQUIRED_HEADERS LayerShellQt_HEADERS
)


generate_export_header(LayerShellQtInterface6
    BASE_NAME LayerShellQtInterface6
    EXPORT_MACRO_NAME LAYERSHELLQT_EXPORT
    EXPORT_FILE_NAME LayerShellQt/layershellqt_export.h
)

install(TARGETS layer-shell6
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}/qt6/plugins/wayland-shell-integration)

install(TARGETS LayerShellQtInterface6 EXPORT LayerShellQtTargets ${KDE_INSTALL_TARGETS_DEFAULT_ARGS})

install(FILES
    ${LayerShellQt_HEADERS}
    ${CMAKE_CURRENT_BINARY_DIR}/LayerShellQt/layershellqt_export.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/LayerShellQt6 COMPONENT Devel
)

add_subdirectory(declarative)
