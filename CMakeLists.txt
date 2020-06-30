project(darling-zlib)

cmake_minimum_required(VERSION 3.0)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -msse -msse2 -msse3 -w -nostdinc")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D__DARWIN_UNIX03 -fPIC -w")
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -nostdlib")

add_definitions(
	# not sure if we should actually define this one; the Xcode build files don't define it, but it wouldn't
	# make too much sense to have it turned off for 64-bit platforms
	-D_LARGEFILE64_SOURCE=1

	-DUSE_MMAP
	-DVEC_OPTIMIZE
)

set(zlib_sources
	zlib/adler32.c
	zlib/compress.c
	zlib/crc32.c
	zlib/deflate.c
	zlib/infback.c
	zlib/inffast.c
	zlib/inflate.c
	zlib/inftrees.c
	zlib/trees.c
	zlib/uncompr.c
	zlib/zutil.c
	zlib/gzclose.c
	zlib/gzlib.c
	zlib/gzread.c
	zlib/gzwrite.c
)

# see zlib/zutil.h, lines 133-144
set_source_files_properties(zlib/zutil.c PROPERTIES
	COMPILE_FLAGS "-Dfdopen=fdopen"
)

set(DYLIB_INSTALL_NAME "/usr/lib/libz.1.dylib")
set(DYLIB_COMPAT_VERSION "1.0.0")
set(DYLIB_CURRENT_VERSION "1.2.11")

add_darling_library(z SHARED ${zlib_sources})
make_fat(z)
target_link_libraries(z system)
set_target_properties(z PROPERTIES LINKER_LANGUAGE C OUTPUT_NAME "z.1")

install(TARGETS z DESTINATION libexec/darling/usr/lib)
foreach(LINK_NAME IN ITEMS "libz.1.1.3.dylib" "libz.1.2.5.dylib" "libz.1.2.8.dylib" "libz.1.2.11.dylib" "libz.dylib")
	InstallSymlink("libz.1.dylib" "${CMAKE_INSTALL_PREFIX}/libexec/darling/usr/lib/${LINK_NAME}")
endforeach()
