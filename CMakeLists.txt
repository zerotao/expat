# This file is copyrighted under the BSD-license for buildsystem files of KDE
# copyright 2010, Patrick Spendrin <ps_ml@gmx.de>

project(expat)

cmake_minimum_required(VERSION 2.6)
set(PACKAGE_BUGREPORT "expat-bugs@libexpat.org")
set(PACKAGE_NAME "expat")
set(PACKAGE_STRING "expat 2.0.1")
set(PACKAGE_TARNAME "expat")
set(PACKAGE_VERSION "2.0.1")

option(BUILD_examples "build the examples for expat library" ON)
option(BUILD_tests "build the tests for expat library" ON)

if(BUILD_tests)
    enable_testing()
endif(BUILD_tests)

include(ConfigureChecks.cmake)

include_directories(${CMAKE_BINARY_DIR} ${CMAKE_SOURCE_DIR}/lib)
if(MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif(MSVC)

set(expat_SRCS
    lib/xmlparse.c
    lib/xmlrole.c
    lib/xmltok.c 
    lib/xmltok_impl.c 
    lib/xmltok_ns.c
)

if(WIN32)
    set(expat_SRCS ${expat_SRCS} lib/libexpat.def)
endif(WIN32)

add_library(expat SHARED ${expat_SRCS})

install(TARGETS expat RUNTIME DESTINATION bin
                      LIBRARY DESTINATION lib
                      ARCHIVE DESTINATION lib)

install(FILES lib/expat.h lib/expat_external.h DESTINATION include)

if(NOT WINCE)
    set(xmlwf_SRCS
        xmlwf/xmlwf.c
        xmlwf/xmlfile.c
        xmlwf/codepage.c
        xmlwf/readfilemap.c
    )

    add_executable(xmlwf ${xmlwf_SRCS})
    target_link_libraries(xmlwf expat)
    install(TARGETS xmlwf DESTINATION bin)
    install(FILES doc/xmlwf.1 DESTINATION share/man/man1)
endif(NOT WINCE)

if(BUILD_examples)
    add_executable(elements examples/elements.c)
    target_link_libraries(elements expat)

    add_executable(outline examples/outline.c)
    target_link_libraries(outline expat)
endif(BUILD_examples)

if(BUILD_tests)
    ## these are unittests that can be run on any platform
    add_executable(runtests tests/runtests.c tests/chardata.c tests/minicheck.c)
    target_link_libraries(runtests expat)
    add_test(runtests runtests)

    add_executable(runtestspp tests/runtestspp.cpp tests/chardata.c tests/minicheck.c)
    target_link_libraries(runtestspp expat)
    add_test(runtestspp runtestspp)
endif(BUILD_tests)
