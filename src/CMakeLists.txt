set(LIBS3_PUBLIC_HEADERS
        ../inc/libs3.h)

add_library(LibS3)
set_target_properties(LibS3
  PROPERTIES
    OUTPUT_NAME s3
    VERSION "${PROJECT_VERSION}"
    PUBLIC_HEADER "${LIBS3_PUBLIC_HEADERS}"
)
target_compile_definitions(LibS3
  PUBLIC
    -DLIBS3_VER_MAJOR="${PROJECT_VERSION_MAJOR}"
    -DLIBS3_VER_MINOR="${PROJECT_VERSION_MINOR}"
    -DLIBS3_VER_PATCH="${PROJECT_VERSION_PATCH}"
    -DLIBS3_VER="${PROJECT_VERSION}"
)
target_sources(LibS3
  PRIVATE
    bucket.c
    bucket_metadata.c
    error_parser.c
    general.c
    md5base64.c
    multipart.c
    object.c
    request.c
    request_context.c
    response_headers_handler.c
    service.c
    service_access_logging.c
    simplexml.c
    util.c
)
if (MINGW)
  target_sources(LibS3
    PRIVATE
      mingw_functions.c
      mingw_s3_functions.c
  )

  if (NOT BUILD_SHARED_LIBS)
    target_compile_definitions(LibS3 PRIVATE -DLIBXML_STATIC)
  endif()
endif()

target_include_directories(LibS3
  PUBLIC
    ${LIBXML2_INCLUDE_DIR}
    ${CURL_INCLUDE_DIR}
    ${OPENSSL_INCLUDE_DIR}
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../inc>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

target_link_libraries(LibS3
  PUBLIC
    OpenSSL::SSL
    ${LIBXML2_LIBRARIES}
    ${CURL_LIBRARIES}
)

add_executable(s3cli s3.c)
set_target_properties(s3cli PROPERTIES OUTPUT_NAME s3)
target_include_directories(s3cli PRIVATE ../inc/)
target_link_libraries(s3cli PRIVATE LibS3)

if (LIBS3_BUILD_TEST)
  add_executable(testsimplexml testsimplexml.c)
  target_include_directories(testsimplexml PRIVATE ../inc/)
  target_link_libraries(testsimplexml PRIVATE LibS3)
endif()

install(
  TARGETS LibS3 s3cli
  EXPORT  ${PROJECT_NAME}
  LIBRARY       DESTINATION "${CMAKE_INSTALL_LIBDIR}"     COMPONENT shlib
  ARCHIVE       DESTINATION "${CMAKE_INSTALL_LIBDIR}"     COMPONENT lib
  RUNTIME       DESTINATION "${CMAKE_INSTALL_BINDIR}"     COMPONENT bin
  PUBLIC_HEADER DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}" COMPONENT dev
)
