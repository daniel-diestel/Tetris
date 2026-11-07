cmake_minimum_required(VERSION 3.16 )

project(TetrisSFML VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(SFML 3 REQUIRED COMPONENTS Graphics Window System Audio)

add_executable(tetris MACOSX_BUNDLE
    src/main.cpp
    src/Board.cpp
    src/Tetromino.cpp
    src/GhostTetromino.cpp
    src/TetrominoBag.cpp
    src/Renderer.cpp
    src/GameState.cpp
    src/UIHelpers.cpp
    src/HighscoreManager.cpp
)

target_include_directories(tetris PRIVATE include)

target_link_libraries(tetris PRIVATE
    SFML::Graphics
    SFML::Window
    SFML::System
    SFML::Audio
)

if(APPLE)

    set_target_properties(tetris PROPERTIES
        MACOSX_BUNDLE_BUNDLE_NAME "Tetris"
        MACOSX_BUNDLE_GUI_IDENTIFIER "com.deinname.tetris"
        MACOSX_BUNDLE_BUNDLE_VERSION "${PROJECT_VERSION}"
        MACOSX_BUNDLE_SHORT_VERSION_STRING "${PROJECT_VERSION}"
        MACOSX_BUNDLE_ICON_FILE "AppIcon.icns"
    )

    set_source_files_properties(
        "${CMAKE_SOURCE_DIR}/AppIcon.icns"
        PROPERTIES MACOSX_PACKAGE_LOCATION "Resources"
    )

    target_sources(tetris PRIVATE
        "${CMAKE_SOURCE_DIR}/AppIcon.icns"
    )

    # Assets in Contents/Resources/assets kopieren
    add_custom_command(TARGET tetris POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        "${CMAKE_SOURCE_DIR}/assets"
        "$<TARGET_BUNDLE_CONTENT_DIR:tetris>/Resources/assets"
    )

    # App Bundle installieren
    install(TARGETS tetris
        BUNDLE DESTINATION .
    )

    install(CODE [[
        include(BundleUtilities)

        set(app "$ENV{DESTDIR}${CMAKE_INSTALL_PREFIX}/tetris.app")

        fixup_bundle(
            "${app}"
            ""
            "/opt/homebrew/opt/sfml/lib"
        )
    ]])

    # DMG erstellen
    set(CPACK_GENERATOR "DragNDrop")
    set(CPACK_PACKAGE_NAME "Tetris")
    set(CPACK_PACKAGE_VERSION "${PROJECT_VERSION}")
    set(CPACK_PACKAGE_FILE_NAME "Tetris-${PROJECT_VERSION}-macOS")

    include(CPack)

endif()
