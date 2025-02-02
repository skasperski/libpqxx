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
add_subdirectory(include)
add_subdirectory(src)

include(CTest)
if(BUILD_TESTING)
    add_subdirectory(test)
endif()

find_program(HAVE_DOXYGEN doxygen)
if(HAVE_DOXYGEN)
    add_subdirectory(doc)
else()
    message(WARNING, "Doxygen not available, skipping documentation.")
endif()

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

# Configure and install pkg-config file
set(prefix ${CMAKE_INSTALL_PREFIX})
set(exec_prefix \${prefix})
set(libdir "\${prefix}/${CMAKE_INSTALL_LIBDIR}")
set(includedir "\${prefix}/${CMAKE_INSTALL_INCLUDEDIR}")
set(VERSION ${PROJECT_VERSION})
configure_file(${PROJECT_SOURCE_DIR}/libpqxx.pc.in ${PROJECT_BINARY_DIR}/libpqxx.pc)
install(
    FILES ${PROJECT_BINARY_DIR}/libpqxx.pc
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig
)

# Package generation
set(CPACK_GENERATOR TGZ)
set(CPACK_PACKAGE_VERSION ${PROJECT_VERSION})
include(CPack)

