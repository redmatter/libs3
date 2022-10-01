cmake_minimum_required(VERSION 3.11)

project(libs3 C)

option(LIBS3_BUILD_TEST "Build libs3 tests." OFF)

# Use BUILD_SHARED_LIBS=OFF build static library instead of shared.
# See https://cmake.org/cmake/help/latest/variable/BUILD_SHARED_LIBS.html

set(LIBS3_VER_MAJOR 4)
set(LIBS3_VER_MINOR 1)

find_package(CURL REQUIRED)
include_directories(SYSTEM ${CURL_INCLUDE_DIRS})

find_package(OpenSSL REQUIRED)
include_directories(SYSTEM ${OPENSSL_INCLUDE_DIR})

find_package(LibXml2 REQUIRED)
include_directories(${LIBXML2_INCLUDE_DIR})

add_library(s3_lib)
set_property(TARGET s3_lib PROPERTY OUTPUT_NAME s3)
target_include_directories(s3_lib PRIVATE inc/)
target_compile_definitions(s3_lib
  PRIVATE
    -DLIBS3_VER_MAJOR="${LIBS3_VER_MAJOR}"
    -DLIBS3_VER_MINOR="${LIBS3_VER_MINOR}"
    -DLIBS3_VER="${LIBS3_VER_MAJOR}.${LIBS3_VER_MINOR}"
)
target_sources(s3_lib
  PRIVATE
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
    src/service.c
    src/service_access_logging.c
    src/simplexml.c
    src/util.c
)
if (MINGW)
  target_sources(s3_lib
    PRIVATE
      src/mingw_functions.c
      src/mingw_s3_functions.c
  )

  if (NOT BUILD_SHARED_LIBS)
    target_compile_definitions(s3_lib PRIVATE -DLIBXML_STATIC)
  endif()
endif()
target_link_libraries(s3_lib
  PRIVATE
    ${LIBXML2_LIBRARIES}
    ${CURL_LIBRARIES}
    ${OPENSSL_LIBRARIES}
)

add_executable(s3_exe src/s3.c)
set_property(TARGET s3_exe PROPERTY OUTPUT_NAME s3)
target_include_directories(s3_exe PRIVATE inc/)
target_link_libraries(s3_exe PRIVATE s3_lib)
target_compile_definitions(s3_exe
  PRIVATE
    -DLIBS3_VER_MAJOR="${LIBS3_VER_MAJOR}"
    -DLIBS3_VER_MINOR="${LIBS3_VER_MINOR}"
    -DLIBS3_VER="${LIBS3_VER_MAJOR}.${LIBS3_VER_MINOR}"
)

if (LIBS3_BUILD_TEST)
  add_executable(testsimplexml src/testsimplexml.c)
  target_include_directories(testsimplexml PRIVATE inc/)
  target_link_libraries(testsimplexml PRIVATE s3_lib)
endif()

