cmake_minimum_required(VERSION 3.20)

project(SteamAudio)

option(SA_BUILD_FLATC "Whether to compile the bundled flatbuffers. If set to OFF (for example, if cross-compiling), then the caller must set FlatBuffers_EXECUTABLE" ON)
option(SA_BUILD_ZLIB "Build the bundled Zlib" ON)

if (SA_BUILD_ZLIB)
    add_subdirectory(zlib EXCLUDE_FROM_ALL)    
	set(ZLIB_LIBRARY zlibstatic)
	set(ZLIB_INCLUDE_DIR "${CMAKE_CURRENT_BINARY_DIR}/zlib" CACHE INTERNAL "")
endif()

set(PFFFT_USE_FFTPACK OFF)
add_subdirectory(pffft EXCLUDE_FROM_ALL)

set(BUILD_TESTS OFF CACHE INTERNAL "")
add_subdirectory(libmysofa EXCLUDE_FROM_ALL)

if (SA_BUILD_FLATC)
    set(FLATBUFFERS_BUILD_TESTS OFF CACHE INTERNAL "")
    set(FLATBUFFERS_INSTALL OFF CACHE INTERNAL "")
    add_subdirectory(flatbuffers EXCLUDE_FROM_ALL)
    if(CMAKE_HOST_WIN32)
        set(FlatBuffers_EXECUTABLE "${CMAKE_CURRENT_BINARY_DIR}/flatbuffers/$<CONFIGURATION>/flatc.exe")
    else()
        set(FlatBuffers_EXECUTABLE "${CMAKE_CURRENT_BINARY_DIR}/flatbuffers/$<CONFIGURATION>/flatc")
    endif()
endif()

set(PFFFT_DIR "${CMAKE_CURRENT_LIST_DIR}/cmake/")
set(MySOFA_DIR "${CMAKE_CURRENT_LIST_DIR}/cmake/")
set(FlatBuffers_DIR "${CMAKE_CURRENT_LIST_DIR}/cmake/")

set(STEAMAUDIO_BUILD_TESTS OFF CACHE INTERNAL "")
set(STEAMAUDIO_BUILD_BENCHMARKS OFF CACHE INTERNAL "")
set(STEAMAUDIO_BUILD_SAMPLES OFF CACHE INTERNAL "")
set(STEAMAUDIO_BUILD_ITESTS OFF CACHE INTERNAL "")
set(STEAMAUDIO_BUILD_DOCS OFF CACHE INTERNAL "")
set(BUILD_SHARED_LIBS OFF CACHE INTERNAL "")

set(PFFFT_LIBRARY pffft)
set(PFFFT_INCLUDE_DIR "${CMAKE_CURRENT_LIST_DIR}/pffft")

set(MySOFA_LIBRARY mysofa-static)
set(MySOFA_INCLUDE_DIR "${CMAKE_CURRENT_LIST_DIR}/libmysofa/src/hrtf")

set(FlatBuffers_INCLUDE_DIR "${CMAKE_CURRENT_LIST_DIR}/flatbuffers/include")
add_subdirectory(steam-audio/core)

add_dependencies(phonon flatc)