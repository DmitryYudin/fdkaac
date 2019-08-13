cmake_minimum_required(VERSION 3.14)

project(fdk-aac-frontend)

source_group(src REGULAR_EXPRESSION ".*\\.[ch].*")

if(CMAKE_CONFIGURATION_TYPES) # https://stackoverflow.com/questions/31661264/cmake-generators-for-visual-studio-do-not-set-cmake-configuration-types
    set(CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "Debug/Release only" FORCE)
endif()

add_subdirectory(../fdk-aac third_party/fdk-aac)

# This stub only required to enable include header as <fdk-aac/aacenc_lib.h> as reference implementation does.
# Since fdk-aac exposes 'libAACenc/include', one can just include <aacenc_lib.h> and not using this stub.
set(SRC_AACENC_LIB_H ${PROJECT_SOURCE_DIR}/../fdk-aac/libAACenc/include/aacenc_lib.h)
set(THIRD_PARTY_INCLUDE_DIR ${PROJECT_BINARY_DIR}/third_party/include)
if(NOT EXISTS ${THIRD_PARTY_INCLUDE_DIR}/fdk-aac)
	file(MAKE_DIRECTORY ${THIRD_PARTY_INCLUDE_DIR}/fdk-aac)
	file(CREATE_LINK ${SRC_AACENC_LIB_H} ${THIRD_PARTY_INCLUDE_DIR}/fdk-aac/aacenc_lib.h)
endif()

set(fdkaac_SRC
    missings/getopt.c
    src/aacenc.c
    src/caf_reader.c
#   src/compat_posix.c
    src/compat_win32.c
    src/extrapolater.c
    src/limiter.c
    src/lpc.c
    src/m4af.c
    src/main.c
    src/metadata.c
    src/parson.c
    src/pcm_float_converter.c
    src/pcm_native_converter.c
    src/pcm_readhelper.c
    src/pcm_sint16_converter.c
    src/progress.c
    src/wav_reader.c
)
add_executable            (fdkaac ${fdkaac_SRC})
target_compile_definitions(fdkaac PRIVATE HAVE_STDINT_H _CRT_SECURE_NO_WARNINGS)
target_include_directories(fdkaac PRIVATE missings . ${THIRD_PARTY_INCLUDE_DIR})
target_link_libraries     (fdkaac fdk-aac)

set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT fdkaac)
