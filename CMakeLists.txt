#!/usr/bin/cmake
cmake_minimum_required (VERSION 3.18)

# -------------------------------------------------------------------------------------------------

project(
  Vorbis
  VERSION 1.3.7
  DESCRIPTION "Lossy audio codec that is free and well-established"
)

# This sets a bunch of compile flags and defined ${NUCLEX_COMPILER_TAG} to
# say something like linux-gcc-13.2-amd64-debug. You should have this directory
# if you do a full clone of a project that is using this third-party library build.
include("../../build-system/cmake/cplusplus.cmake")

# -------------------------------------------------------------------------------------------------

if(NOT EXISTS ${PROJECT_SOURCE_DIR}/build)
  message(STATUS "Build directory not present, downloading sources")

  file(
    DOWNLOAD https://github.com/xiph/vorbis/releases/download/v1.3.7/libvorbis-1.3.7.tar.gz
    ${PROJECT_SOURCE_DIR}/downloads/libvorbis-1.3.7.tar.gz
    SHOW_PROGRESS
    EXPECTED_HASH SHA256=0e982409a9c3fc82ee06e08205b1355e5c6aa4c36bca58146ef399621b0ce5ab
    STATUS DOWNLOAD_STATUS
  )
  list(GET DOWNLOAD_STATUS 0 STATUS_CODE)
  if(NOT ${STATUS_CODE} EQUAL 0)
    list(GET DOWNLOAD_STATUS 1 ERROR_MESSAGE)
    list(GET DOWNLOAD_STATUS 2 HTTP_CODE)
    message(
      FATAL_ERROR
      "Could not download Vorbis sources: ${ERROR_MESSAGE} (HTTP result ${HTTP_CODE})"
    )
  endif()

  message(STATUS "Extracting Vorbis sources")
  file(
    ARCHIVE_EXTRACT
    INPUT ${PROJECT_SOURCE_DIR}/downloads/libvorbis-1.3.7.tar.gz
    DESTINATION ${CMAKE_BINARY_DIR}/extract
  )
  file(
    RENAME
    ${CMAKE_BINARY_DIR}/extract/libvorbis-1.3.7
    ${PROJECT_SOURCE_DIR}/build
  )
endif()

# -------------------------------------------------------------------------------------------------

configure_file(
  "${PROJECT_SOURCE_DIR}/config.h"
  "${PROJECT_SOURCE_DIR}/build/lib/config.h"
  COPYONLY
)

# -------------------------------------------------------------------------------------------------

set(
  vorbisHeaderFiles
  "build/include/vorbis/codec.h"
)
set(
  vorbisFileHeaderFiles
  "build/include/vorbis/vorbisfile.h"
)
set(
  vorbisEncHeaderFiles
  "build/include/vorbis/vorbisenc.h"
)

set(
  vorbisSourceFiles
  "build/lib/smallft.c"
  "build/lib/lpc.c"
  "build/lib/analysis.c"
  "build/lib/block.c"
  "build/lib/envelope.c"
  "build/lib/info.c"
  "build/lib/mdct.c"
  "build/lib/lsp.c"
  "build/lib/synthesis.c"
  "build/lib/window.c"
  "build/lib/floor1.c"
  "build/lib/floor0.c"
  "build/lib/res0.c"
  "build/lib/mapping0.c"
  "build/lib/psy.c"
  "build/lib/registry.c"
  "build/lib/codebook.c"
  "build/lib/sharedbook.c"
  "build/lib/lookup.c"
  "build/lib/bitrate.c"
)
set(
  vorbisFileSourceFiles
  "build/lib/vorbisfile.c"
)
set(
  vorbisEncSourceFiles
  "build/lib/vorbisenc.c"
)

# -------------------------------------------------------------------------------------------------

add_library(Vorbis STATIC)
add_library(Vorbis::Static ALIAS Vorbis)

add_library(VorbisFile STATIC)
add_library(VorbisFile::Static ALIAS VorbisFile)

add_library(VorbisEnc STATIC)
add_library(VorbisEnc::Static ALIAS VorbisEnc)

target_compile_definitions(
  Vorbis
  PUBLIC OV_EXCLUDE_STATIC_CALLBACKS
  PRIVATE HAVE_CONFIG_H
)
target_compile_definitions(
  VorbisFile
  PUBLIC OV_EXCLUDE_STATIC_CALLBACKS
  PRIVATE HAVE_CONFIG_H
)
target_compile_definitions(
  VorbisEnc
  PUBLIC OV_EXCLUDE_STATIC_CALLBACKS
  PRIVATE HAVE_CONFIG_H
)

target_include_directories(
  Vorbis
  PUBLIC "build/include"
  PRIVATE "build/lib"
)
target_include_directories(
  VorbisFile
  PUBLIC "build/include"
  PRIVATE "build/lib"
)
target_include_directories(
  VorbisEnc
  PUBLIC "build/include"
  PRIVATE "build/lib"
)

target_sources(
  Vorbis
  PUBLIC ${vorbisHeaderFiles}
  PRIVATE ${vorbisSourceFiles}
)
target_sources(
  VorbisFile
  PUBLIC ${vorbisFileHeaderFiles}
  PRIVATE ${vorbisFileSourceFiles}
)
target_sources(
  VorbisEnc
  PUBLIC ${vorbisEncHeaderFiles}
  PRIVATE ${vorbisEncSourceFiles}
)

set_target_properties(Vorbis PROPERTIES PREFIX "")
set_target_properties(Vorbis PROPERTIES OUTPUT_NAME "vorbis")

set_target_properties(VorbisFile PROPERTIES PREFIX "")
set_target_properties(VorbisFile PROPERTIES OUTPUT_NAME "vorbisfile")

set_target_properties(VorbisEnc PROPERTIES PREFIX "")
set_target_properties(VorbisEnc PROPERTIES OUTPUT_NAME "vorbisenc")

# -------------------------------------------------------------------------------------------------

install(
  TARGETS Vorbis
  ARCHIVE DESTINATION ${PROJECT_SOURCE_DIR}/bin/${NUCLEX_COMPILER_TAG}
)
install(
  TARGETS VorbisFile
  ARCHIVE DESTINATION ${PROJECT_SOURCE_DIR}/bin/${NUCLEX_COMPILER_TAG}
)
install(
  TARGETS VorbisEnc
  ARCHIVE DESTINATION ${PROJECT_SOURCE_DIR}/bin/${NUCLEX_COMPILER_TAG}
)
