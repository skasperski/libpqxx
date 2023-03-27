cmake_minimum_required(VERSION 3.8)
include(GNUInstallDirs)

# Read pqxx version from file 'VERSION'
file(READ VERSION VER_FILE_CONTENT)
string(STRIP ${VER_FILE_CONTENT} VER_FILE_CONTENT)

project(
    libpqxx
    VERSION ${VER_FILE_CONTENT}
    LANGUAGES CXX
)

find_package(PkgConfig REQUIRED)
pkg_check_modules(PostgreSQL REQUIRED IMPORTED_TARGET libpq)

include(cmake/config.cmake)
add_subdirectory(src)

# Create and install the version file
include(CMakePackageConfigHelpers)
write_basic_package_version_file("libpqxx-config-version.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

install(
    FILES
        cmake/libpqxx-config.cmake
        "${CMAKE_CURRENT_BINARY_DIR}/libpqxx-config-version.cmake"
    DESTINATION
        ${CMAKE_INSTALL_LIBDIR}/cmake/libpqxx
)

install(
    EXPORT libpqxx-targets
    NAMESPACE libpqxx::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/libpqxx
)

# Build tree export
export(
    EXPORT libpqxx-targets
    NAMESPACE libpqxx::
    FILE ${CMAKE_CURRENT_BINARY_DIR}/libpqxx-targets.cmake
)

# Package generation
#set(CPACK_GENERATOR TGZ)
#set(CPACK_PACKAGE_VERSION ${PROJECT_VERSION})
#include(CPack)

