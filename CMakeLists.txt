cmake_minimum_required(VERSION 3.14)

project(basisu_transcoder)

set( BASIS_UNIVERSAL_UNITY_PATH "" CACHE PATH "Path locating the BasisUniversalUnity package source. When installing, native libraries will get injected there" )

set( DEST_PLUGIN_PATH "${BASIS_UNIVERSAL_UNITY_PATH}/Runtime/Plugins")

set(bitness 64)
if(NOT CMAKE_SIZEOF_VOID_P EQUAL 8 OR FORCE32)
	set(bitness 32)
endif()

if(ANDROID)
set(UNITY_PLUGIN_DIR ${DEST_PLUGIN_PATH}/Android/libs/${ANDROID_ABI})
elseif(IOS)
set(UNITY_PLUGIN_DIR ${DEST_PLUGIN_PATH}/iOS)
else()
if(bitness EQUAL 32)
set(UNITY_PLUGIN_DIR ${DEST_PLUGIN_PATH}/x86)
else()
set(UNITY_PLUGIN_DIR ${DEST_PLUGIN_PATH}/x86_64)
endif()
endif()

set(CMAKE_CXX_FLAGS -std=c++11)
set(CMAKE_CXX_STANDARD 11)

set(UNITY_PLUGIN_DIR_WEBGL ${DEST_PLUGIN_PATH}/WebGL)

message(STATUS "Will install native libs to ${UNITY_PLUGIN_DIR}")
message(STATUS "Will copy source to ${UNITY_PLUGIN_DIR_WEBGL} for WebGL builds")

if( NOT EXISTS ${UNITY_PLUGIN_DIR}
	OR NOT EXISTS "${UNITY_PLUGIN_DIR_WEBGL}"
)
	message(SEND_ERROR "Invalid BasisUniversalUnity path!")
endif()

if(APPLE)
    # This little macro lets you set any XCode specific property
    macro (set_xcode_property TARGET XCODE_PROPERTY XCODE_VALUE)
        set_property (TARGET ${TARGET} PROPERTY XCODE_ATTRIBUTE_${XCODE_PROPERTY} ${XCODE_VALUE})
    endmacro (set_xcode_property)
endif(APPLE)

set(BASISU_SRC_LIST
	basis_universal/transcoder/basisu_transcoder.cpp
	basisu_wrapper/basisu_wrapper.cpp
	)

if( IOS )
	set( LibType STATIC )
else()
	# Unity Editor (on macOS) requires a dynamic library.
	set( LibType SHARED )
endif()

add_library(basisu ${LibType} ${BASISU_SRC_LIST})

target_include_directories(
	basisu
	PUBLIC
	basis_universal/transcoder
)

if(WIN32)
	target_compile_definitions(
		basisu
		PRIVATE
		DLL_EXPORT_FLAG
		)
endif()

if(APPLE AND NOT IOS)
	# Unity Editor need the library in .bundle format:
	set (CMAKE_SHARED_LIBRARY_PREFIX "")
    set (CMAKE_SHARED_MODULE_PREFIX "")

    set (CMAKE_SHARED_LIBRARY_SUFFIX ".bundle")
    
	set_xcode_property (basisu EXECUTABLE_SUFFIX ".bundle")
endif()

install(TARGETS basisu DESTINATION ${UNITY_PLUGIN_DIR})

install(
FILES
	basis_universal/transcoder/basisu_transcoder.h
	basis_universal/transcoder/basisu_transcoder_internal.h
	basis_universal/transcoder/basisu_global_selector_cb.h
	basis_universal/transcoder/basisu_transcoder.cpp
	basis_universal/transcoder/basisu_transcoder_tables_bc7_m6.inc
	basis_universal/transcoder/basisu_global_selector_palette.h
	basis_universal/transcoder/basisu.h
	basis_universal/transcoder/basisu_transcoder_tables_dxt1_6.inc
	basis_universal/transcoder/basisu_file_headers.h
	basis_universal/transcoder/basisu_transcoder_tables_dxt1_5.inc
	basisu_wrapper/basisu_wrapper.cpp
DESTINATION
	${UNITY_PLUGIN_DIR_WEBGL}
)
