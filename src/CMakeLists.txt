# Depending on the platform, the aescrypt binary is expected to be in /usr/bin
# or /usr/local/bin, so set the aescrypt.desktop file accordingly
if(BSD OR APPLE)
    set(INSTALL_PATH "/usr/local")
else()
    set(INSTALL_PATH "/usr")
endif()

# Create the aescrypt.desktop file with expected install paths
configure_file(aescrypt.desktop.in aescrypt.desktop @ONLY)

if(aescrypt_gui_INSTALL AND (UNIX AND NOT APPLE))
    include(GNUInstallDirs)
    install(PROGRAMS aescrypt-gui DESTINATION ${CMAKE_INSTALL_BINDIR})
    install(FILES ${PROJECT_BINARY_DIR}/src/aescrypt.desktop DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/applications/)
    install(FILES aescrypt_lock.svg DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/icons/hicolor/scalable/apps/)
    install(FILES aescrypt_paper.svg DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/icons/hicolor/scalable/apps/)
    install(FILES aescrypt.xml DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/mime/packages/)
endif()
