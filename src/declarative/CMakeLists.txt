# SPDX-FileCopyrightText: 2021 Aleix Pol Gonzalez <aleix.pol_gonzalez@mercedes-benz.com>
# SPDX-License-Identifier: BSD-3-Clause

qt6_add_qml_module(LayerShellQtQml6
                  URI "LayerShell"
                  VERSION 1.0
                  SOURCES layershellqtplugin.cpp)
target_link_libraries(LayerShellQtQml6 PRIVATE Qt::Qml LayerShellQtInterface6)

#ecm_finalize_qml_module(LayerShellQtQml6 DESTINATION ${KDE_INSTALL_QMLDIR})
install(TARGETS LayerShellQtQml6
    RUNTIME DESTINATION "${CMAKE_INSTALL_LIBDIR}/qt6/qml/LayerShellQtQml"
    BUNDLE DESTINATION "${CMAKE_INSTALL_LIBDIR}/qt6/qml/LayerShellQtQml"
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}/qt6/qml/LayerShellQtQml"
)

install(
    FILES ${CMAKE_CURRENT_BINARY_DIR}/qmldir
            ${CMAKE_CURRENT_BINARY_DIR}/LayerShellQtQml6.qmltypes
    DESTINATION "${CMAKE_INSTALL_LIBDIR}/qt6/qml/LayerShellQtQml")