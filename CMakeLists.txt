cmake_minimum_required(VERSION 3.0.0)

project(nzmqt VERSION 3.2.1)

set(QT_VERSION 5.15.0 CACHE STRING "QT version")
set(QT_VER ${QT_VERSION})
set(QT_PATH /opt/qt/${QT_VER}/lib/cmake/Qt5 CACHE PATH "Path for QT cmake")
set(Qt5_DIR ${QT_PATH})

find_package(cppzmq REQUIRED)
find_package(ZeroMQ REQUIRED)
find_package(Qt5 REQUIRED COMPONENTS Core)

# Needed for QT
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# Build shared library
add_library(nzmqt SHARED "")

target_include_directories(
    nzmqt
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include>
    ${Qt5Core_INCLUDE_DIRS}
)

target_compile_definitions(
    nzmqt
    PUBLIC
    ZMQ_BUILD_DRAFT_API
    NZMQT_LIB
    NZMQT_SHAREDLIB
)

target_sources(
    nzmqt
    PRIVATE
    src/nzmqt/nzmqt.cpp
    include/nzmqt/global.hpp
    include/nzmqt/impl.hpp
    include/nzmqt/nzmqt.hpp
)

target_link_libraries(
    nzmqt
    PUBLIC
    zmq
    cppzmq
    Qt5::Core
)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

set(
    NZMQT_CONFIG_INSTALL_DIR
    "${CMAKE_INSTALL_DATADIR}/cmake/${PROJECT_NAME}" CACHE STRING
    "Install path for nzmqtConfig.cmake"
)

configure_package_config_file(
    ${PROJECT_NAME}Config.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    INSTALL_DESTINATION ${NZMQT_CONFIG_INSTALL_DIR}
)

install(
    TARGETS nzmqt
    EXPORT ${PROJECT_NAME}-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(
    FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
    DESTINATION ${NZMQT_CONFIG_INSTALL_DIR}
)

install(
    EXPORT ${PROJECT_NAME}-targets
    FILE ${PROJECT_NAME}Targets.cmake
    DESTINATION ${NZMQT_CONFIG_INSTALL_DIR}
)

install(
    DIRECTORY include/nzmqt
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

export(
    EXPORT ${PROJECT_NAME}-targets
    FILE "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Targets.cmake"
)
