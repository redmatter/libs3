cmake_minimum_required(VERSION 3.13)
project(libs3
        VERSION 4.1.0
        LANGUAGES C)

option(LIBS3_BUILD_TEST "Build libs3 tests." ON)
option(BUILD_SHARED_LIBS "Build shared library" ON)

include(GNUInstallDirs)

find_package(CURL REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(LibXml2 REQUIRED)

add_subdirectory(src)
