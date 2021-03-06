
cmake_minimum_required (VERSION 3.4)
project (LASTOOLS)

set (LAS_SRC LASlib/src/lasreader.cpp
    LASlib/src/laswriter.cpp 
    LASlib/src/lasreader_las.cpp
    LASlib/src/lasreader_bin.cpp 
    LASlib/src/lasreader_qfit.cpp
    LASlib/src/lasreader_shp.cpp 
    LASlib/src/lasreader_asc.cpp 
    LASlib/src/lasreader_bil.cpp 
    LASlib/src/lasreader_dtm.cpp 
    LASlib/src/lasreader_txt.cpp
    LASlib/src/lasreadermerged.cpp 
    LASlib/src/lasreaderbuffered.cpp 
    LASlib/src/lasreaderpipeon.cpp 
    LASlib/src/laswriter_las.cpp 
    LASlib/src/laswriter_bin.cpp
    LASlib/src/laswriter_qfit.cpp
    LASlib/src/laswriter_wrl.cpp 
    LASlib/src/laswriter_txt.cpp 
    LASlib/src/laswritercompatible.cpp
    LASlib/src/laswaveform13reader.cpp
    LASlib/src/laswaveform13writer.cpp 
    LASlib/src/lasutility.cpp 
    LASlib/src/lasfilter.cpp 
    LASlib/src/lastransform.cpp 
    LASlib/src/fopen_compressed.cpp)

set (LAZ_SRC LASzip/src/laszip.cpp
    LASzip/src/lasreadpoint.cpp 
    LASzip/src/lasreaditemcompressed_v1.cpp 
    LASzip/src/lasreaditemcompressed_v2.cpp
    LASzip/src/lasreaditemcompressed_v3.cpp
    LASzip/src/lasreaditemcompressed_v4.cpp
    LASzip/src/laswritepoint.cpp  
    LASzip/src/laswriteitemcompressed_v1.cpp 
    LASzip/src/laswriteitemcompressed_v2.cpp 
    LASzip/src/laswriteitemcompressed_v3.cpp 
    LASzip/src/laswriteitemcompressed_v4.cpp
    LASzip/src/integercompressor.cpp
    LASzip/src/arithmeticdecoder.cpp 
    LASzip/src/arithmeticencoder.cpp 
    LASzip/src/arithmeticmodel.cpp 
    LASzip/src/lasindex.cpp 
    LASzip/src/lasquadtree.cpp 
    LASzip/src/lasinterval.cpp)

add_library (las SHARED ${LAS_SRC} ${LAZ_SRC})
target_include_directories (las PUBLIC LASlib/inc LASzip/src)
# the LASLIB_DLL macro doesn't actually export everything required
set_target_properties(las PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS TRUE)

add_definitions(-D_CRT_SECURE_NO_WARNINGS)
add_definitions(-D_SILENCE_STDEXT_HASH_DEPRECATION_WARNINGS)
add_definitions(-DNDEBUG)
if( NOT MSVC OR MSVC_VERSION GREATER 1700)
    # gcc etc or later versions of MSVC support unordered_map
    add_definitions(-DUNORDERED)
    add_definitions(-DHAVE_UNORDERED_MAP) # needed for older clang for some reason
	if( NOT MSVC )
		# gcc etc need this to enable unordered_map
		add_definitions(-std=c++11)
    endif()
	message(STATUS "Using unordered_map")
else()
	message(STATUS "NOT Using unordered_map")
endif()

add_executable (laszip src/laszip.cpp src/geoprojectionconverter.cpp)
target_link_libraries (laszip las)
add_executable (lasinfo src/lasinfo.cpp src/geoprojectionconverter.cpp)
target_link_libraries (lasinfo las)
add_executable (lasprecision src/lasprecision.cpp src/geoprojectionconverter.cpp)
target_link_libraries (lasprecision las)
add_executable (las2txt src/las2txt.cpp)
target_link_libraries (las2txt las)
add_executable (txt2las src/txt2las.cpp src/geoprojectionconverter.cpp)
target_link_libraries (txt2las las)
add_executable (las2las src/las2las.cpp src/geoprojectionconverter.cpp)
target_link_libraries (las2las las)
add_executable (lasdiff src/lasdiff.cpp)
target_link_libraries (lasdiff las)
add_executable (lasmerge src/lasmerge.cpp src/geoprojectionconverter.cpp)
target_link_libraries (lasmerge las)
add_executable (lasindex src/lasindex.cpp src/geoprojectionconverter.cpp)
target_link_libraries (lasindex las)

install(TARGETS las laszip lasinfo lasprecision las2txt txt2las las2las lasdiff lasmerge lasindex
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib)

FILE(GLOB LIB_INC LASlib/inc/*)  
FILE(GLOB LAZ_INC LASzip/src/*.hpp)  
install(FILES ${LIB_INC} DESTINATION include)
install(FILES ${LAZ_INC} DESTINATION include)
