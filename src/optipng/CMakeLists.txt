cmake_minimum_required(VERSION 4.0 FATAL_ERROR)
project(optipng LANGUAGES C CXX)

add_library(optipng
	optipng.cpp
	codec.cpp
	image.c
	opngreduc/opngreduc.c

	codec.h
	image.h
	opngcore.h
	opngreduc/opngreduc.h
	trans.h)

add_library(optipng::optipng ALIAS optipng)

#make sure that we are using custom zlib and custom libpng options
set(ZLIB_ROOT "${CMAKE_BINARY_DIR}/zlib" "use custom zlib within libpng" FORCE)
set(PNG_ARM_NEON "on" CACHE STRING "Enable ARM NEON optimizations: on|off; on is default" FORCE)
set(ZLIB_INCLUDE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}/../zlib/ CACHE FILEPATH "custom zlib directory" FORCE)

if(NOT WIN32)
	add_compile_options(-Wno-macro-redefined)
endif()
add_compile_definitions(PNG_USER_CONFIG)

#silence libpng warnings
if(NOT MSVC)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-null-pointer-subtraction")
endif()

add_subdirectory(../libpng libpng EXCLUDE_FROM_ALL)
target_link_libraries(optipng
	png_static)

# libpng generates some header files that we need to be able to include
target_include_directories(optipng
	PRIVATE
		${CMAKE_CURRENT_BINARY_DIR}/libpng)
