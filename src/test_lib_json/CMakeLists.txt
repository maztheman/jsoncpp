# vim: et ts=4 sts=4 sw=4 tw=0

add_executable(jsoncpp_test
    jsontest.cpp
    jsontest.h
    fuzz.cpp
    fuzz.h
    main.cpp
)


if(BUILD_SHARED_LIBS)
    if(CMAKE_VERSION VERSION_GREATER_EQUAL 3.12.0)
        target_compile_definitions(jsoncpp_test PRIVATE JSON_DLL )
    else()
        target_compile_definitions(jsoncpp_test PRIVATE -DJSON_DLL )
    endif()
    target_link_libraries(jsoncpp_test jsoncpp_lib
        $<BUILD_INTERFACE:project_options>
    )
    else()
    target_link_libraries(jsoncpp_test jsoncpp_static $<BUILD_INTERFACE:project_options>)
endif()

# another way to solve issue #90
#set_target_properties(jsoncpp_test PROPERTIES COMPILE_FLAGS -ffloat-store)

## Create tests for dashboard submission, allows easy review of CI results https://my.cdash.org/index.php?project=jsoncpp
add_test(NAME jsoncpp_test
    COMMAND ${CMAKE_CROSSCOMPILING_EMULATOR} $<TARGET_FILE:jsoncpp_test>
)
set_target_properties(jsoncpp_test PROPERTIES OUTPUT_NAME jsoncpp_test)

# Run unit tests in post-build
# (default cmake workflow hides away the test result into a file, resulting in poor dev workflow?!?)
if(JSONCPP_WITH_POST_BUILD_UNITTEST)
    add_custom_command(TARGET jsoncpp_test
        POST_BUILD
        COMMAND ${CMAKE_CROSSCOMPILING_EMULATOR} $<TARGET_FILE:jsoncpp_test>
    )
endif()
