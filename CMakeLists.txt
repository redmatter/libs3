cmake_minimum_required(VERSION 3.11)

project(libs3 C)

set(LIBS3_VER_MAJOR 4)
set(LIBS3_VER_MINOR 1)

add_definitions(-DLIBS3_VER_MAJOR="${LIBS3_VER_MAJOR}")
add_definitions(-DLIBS3_VER_MINOR="${LIBS3_VER_MINOR}")
add_definitions(-DLIBS3_VER="${LIBS3_VER_MAJOR}.${LIBS3_VER_MINOR}")

find_package(CURL REQUIRED)
include_directories(SYSTEM ${CURL_INCLUDE_DIRS})

find_package(OpenSSL REQUIRED)
include_directories(SYSTEM ${OPENSSL_INCLUDE_DIR})

find_package(LibXml2 REQUIRED)
include_directories(${LIBXML2_INCLUDE_DIR})

if (MINGW)
  add_definitions(-DLIBXML_STATIC)
endif()

include_directories(inc/)

set(LIBS3_SOURCES
  src/bucket.c
  src/bucket_metadata.c
  src/error_parser.c
  src/general.c
  src/md5base64.c
  src/multipart.c
  src/object.c
  src/request.c
  src/request_context.c
  src/response_headers_handler.c
  src/s3.c
  src/service.c
  src/service_access_logging.c
  src/simplexml.c
  src/testsimplexml.c
  src/util.c
)

if (MINGW)
  string(APPEND LIBS3_SOURCES
    src/mingw_functions.c
    src/mingw_s3_functions.c
  )
endif()

add_library(s3 STATIC ${LIBS3_SOURCES})
target_link_libraries(
  s3
  ${LIBXML2_LIBRARIES}
  ${CURL_LIBRARIES}
  ${OPENSSL_LIBRARIES}
)
