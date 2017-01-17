project(darling-zlib)

cmake_minimum_required(VERSION 2.4.0)
enable_language(C ASM)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -msse -msse2 -msse3 -w -nostdinc")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D__DARWIN_UNIX03 -fPIC -w")
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -nostdlib")

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/)
include_directories(${DARLING_TOP_DIRECTORY}/platform-include)
add_definitions(-D_LARGEFILE64_SOURCE=1 -DZ_HAVE_UNISTD_H -DASMV)

set(zlib_sources
	adler32.c
	compress.c
	crc32.c
	deflate.c
	gzclose.c
	gzlib.c
	gzread.c
	gzwrite.c
	infback.c
	inffast.c
	inflate.c
	inftrees.c
	trees.c
	uncompr.c
	zutil.c
)

if (BITS EQUAL 64)
	set(zlib_sources ${zlib_sources} contrib/amd64/amd64-match.S)
else(BITS EQUAL 64)
	set(zlib_sources ${zlib_sources} contrib/asm686/match.S)
endif(BITS EQUAL 64)

set(DYLIB_INSTALL_NAME "/usr/lib/libz.1.dylib")
add_darling_library(z SHARED ${zlib_sources})
target_link_libraries(z system)
set_target_properties(z PROPERTIES LINKER_LANGUAGE C OUTPUT_NAME "z.1")

install(TARGETS z DESTINATION libexec/darling/usr/lib)
