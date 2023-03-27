file(GLOB CXX_SOURCES *.cxx)
add_library(pqxx SHARED ${CXX_SOURCES})

target_include_directories(pqxx
    PUBLIC
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
        $<BUILD_INTERFACE:${PROJECT_BINARY_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

target_compile_features(pqxx PUBLIC cxx_std_17)

target_link_libraries(pqxx
    PRIVATE
        PkgConfig::PostgreSQL
)

install(TARGETS pqxx EXPORT libpqxx-targets
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

