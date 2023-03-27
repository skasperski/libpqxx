file(GLOB TEST_SOURCES test*.cxx unit/test_*.cxx runner.cxx)
add_executable(runner ${TEST_SOURCES})
target_link_libraries(runner PUBLIC pqxx)

add_test(
    NAME runner
    COMMAND runner
)

