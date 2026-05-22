# This file facilitates creation of package files for OS installation
if(aescrypt_gui_INSTALL AND (UNIX AND NOT APPLE))
    include(GNUInstallDirs)
    include(InstallRequiredSystemLibraries)
    set(CPACK_PACKAGE_NAME "${PROJECT_NAME}")
    set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "AES Crypt GUI")
    set(CPACK_PACKAGE_DESCRIPTION_FILE "${CMAKE_CURRENT_SOURCE_DIR}/description.txt")
    set(CPACK_PACKAGE_VENDOR "${AUTHOR_TEXT}")
    set(CPACK_PACKAGE_CONTACT "support@terrapane.com")
    set(CPACK_RESOURCE_FILE_LICENSE "${PROJECT_SOURCE_DIR}/LICENSE.md")
    set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
    set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
    set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")
    set(CPACK_PACKAGE_VERSION_TWEAK "${PROJECT_VERSION_TWEAK}")
    set(CPACK_PACKAGE_VERSION "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH}")
    if(DEFINED PROJECT_VERSION_TWEAK AND PROJECT_VERSION_TWEAK GREATER 0)
        set(CPACK_PACKAGE_VERSION "${CPACK_PACKAGE_VERSION}.${PROJECT_VERSION_TWEAK}")
    endif()
    set(CPACK_VERBATIM_VARIABLES TRUE)
    set(CPACK_PACKAGE_FILE_NAME ${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_PROCESSOR})
    if(LINUX)
        set(CPACK_RPM_PACKAGE_RELEASE 1)
        set(CPACK_RPM_PACKAGE_GROUP "Applications/Utilities")
        set(CPACK_RPM_PACKAGE_LICENSE "Proprietary")
        execute_process(COMMAND rpm --version OUTPUT_VARIABLE RPM_VERSION OUTPUT_STRIP_TRAILING_WHITESPACE)
        string(REGEX MATCH "[0-9]+\\.[0-9]+" RPM_MAJOR_MINOR "${RPM_VERSION}")
        # Only RPM 4.13 or later supports "or" syntax
        if(RPM_MAJOR_MINOR VERSION_GREATER_EQUAL "4.13")
            set(CPACK_RPM_PACKAGE_REQUIRES "glibc, libstdc++, (perl >= 5.10 or perl-interpreter >= 5.10), (zenity >= 3.0 or kdialog or /usr/bin/kdialog or /usr/local/bin/kdialog)")
        else()
            set(CPACK_RPM_PACKAGE_REQUIRES "glibc, libstdc++, perl >= 5.10, zenity >= 3.0")
        endif()
        set(CPACK_RPM_PACKAGE_ICON "${CMAKE_SOURCE_DIR}/src/aescrypt_lock.svg")
        set(CPACK_DEBIAN_PACKAGE_SECTION "utils")
        set(CPACK_DEBIAN_PACKAGE_DEPENDS "libc6, libstdc++6, perl (>= 5.10) | perl-base (>= 5.10), zenity (>= 3.0) | kdialog")
        set(CPACK_GENERATOR RPM DEB TGZ)
    else()
        set(CPACK_GENERATOR TGZ)
    endif()
    include(CPack)

    # Create the metainfo file and install it
    file(READ "${CMAKE_CURRENT_SOURCE_DIR}/description.txt" DESCRIPTION_TEXT)
    string(REPLACE ";" "\\;" DESCRIPTION_TEXT "${DESCRIPTION_TEXT}")
    string(REGEX REPLACE "\n$" "" DESCRIPTION_TEXT "${DESCRIPTION_TEXT}")
    configure_file(metainfo.xml.in com.terrapane.${PROJECT_NAME}.metainfo.xml @ONLY)
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/com.terrapane.${PROJECT_NAME}.metainfo.xml DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/applications/)

    # Note the dependency so that the description text is never out-of-sync
    set_property(
        DIRECTORY
        APPEND
        PROPERTY CMAKE_CONFIGURE_DEPENDS
            "${CMAKE_CURRENT_SOURCE_DIR}/description.txt"
    )
endif()
