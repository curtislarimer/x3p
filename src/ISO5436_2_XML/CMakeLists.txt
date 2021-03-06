project(ISO5436_2)

cmake_minimum_required(VERSION 2.8)

if(NOT DEFINED BUILD_SHARED_LIBS)
	OPTION(BUILD_SHARED_LIBS "Build shared library." OFF)
endif(NOT DEFINED BUILD_SHARED_LIBS)


IF(BUILD_SHARED_LIBS)
  SET(LIBRARY_TYPE SHARED)
  add_definitions(-DZLIB_DLL -D_DLL -DBUILD_ISO5436_2_XML_DLL)
ELSE(BUILD_SHARED_LIBS)
  SET(LIBRARY_TYPE STATIC)
  add_definitions(-D_LIB) 
  SET(iso54362LIBSUFFIX S)
ENDIF(BUILD_SHARED_LIBS)

if(CMAKE_CL_64)
	SET(iso54362LIBSUFFIX ${iso54362LIBSUFFIX}64)
endif(CMAKE_CL_64)
SET (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR})

SET(iso54362xmlNAME iso5436-2-xml${iso54362LIBSUFFIX})


find_package(XSD REQUIRED)

XSD_SCHEMA(OUTPATH ${PROJECT_SOURCE_DIR}/iso5436_2.xsd  --prologue-file ${PROJECT_SOURCE_DIR}/xsd_Licence_Header.c --generate-doxygen --generate-ostream --generate-serialization --char-type wchar_t --generate-comparison --generate-from-base-ctor --namespace-map http://www.opengps.eu/2008/ISO5436_2=OpenGPS::Schemas::ISO5436_2 --export-symbol _OPENGPS_EXPORT --cxx-suffix \"_xsd.cxx\" --hxx-suffix \"_xsd.hxx\" --output-dir ${PROJECT_SOURCE_DIR}/../../include/opengps/cxx/)

find_package(Xerces REQUIRED)

# Subversion is needed to generate version.h from version.in
#FIND_PACKAGE(Subversion)
#IF (NOT SUBVERSION_FOUND)
#   FIND_PACKAGE(TortoiseSVN REQUIRED)
#   MESSAGE(STATUS "Using TortoiseSVN from:")
#   MESSAGE(STATUS "   " ${TortoiseSVN_EXECUTABLE})
#   SET(Subversion_SVN_EXECUTABLE ${TortoiseSVN_EXECUTABLE})
#ENDIF(NOT SUBVERSION_FOUND)
# Find native ZLIB package
# FIND_PACKAGE(ZLIB REQUIRED)


add_definitions(-DUNICODE -D_UNICODE -DISO5436_2_LIBRARY -DBUILD_ISO5436_2_XML  -DXSD_FP_ALL_DIGITS)
if (WIN32 AND NOT BUILD_SHARED_LIBS)
   add_definitions(-DZLIB_WINAPI)
endif()

# default build types are None, Debug, Release, RelWithDebInfo and MinRelSize
IF (DEFINED CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE} CACHE STRING "Choose the type of build, options are: None(CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel.")
ELSE(CMAKE_BUILD_TYPE)
  SET (CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build,
options are: None(CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used) Debug Release
RelWithDebInfo MinSizeRel.")
ENDIF (DEFINED CMAKE_BUILD_TYPE)

INCLUDE_DIRECTORIES( "${PROJECT_SOURCE_DIR}/../../include"
	  "${PROJECT_SOURCE_DIR}/../../../../include/opengps/cxx"
	  "${PROJECT_SOURCE_DIR}/../zlib"
	  "${PROJECT_SOURCE_DIR}/../zlib/contrib/minizip"
	  "${XSD_INCLUDE_DIR}"
	  "${XERCESC_INCLUDE}"
)

# Generate the version include file from the .in file
#if (WIN32)
## Find subwcrev executable
#GET_FILENAME_COMPONENT(Subversion_PATH "${Subversion_SVN_EXECUTABLE}" PATH)
#SET(Subwcrev_EXECUTABLE "${Subversion_PATH}/subwcrev.exe" )
#MESSAGE(STATUS Subwcrev found: ${Subwcrev_EXECUTABLE})
## subwcrev $(ProjectDir) $(InputDir)/cxx/Version.in $(InputDir)/cxx/Version.h
#ADD_CUSTOM_COMMAND(OUTPUT "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.h"
#                    PRE_BUILD
#                    COMMAND "${Subwcrev_EXECUTABLE}"
#                    ARGS "${PROJECT_SOURCE_DIR}"
#                         "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.in"
#                         "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.h"
#                    MAIN_DEPENDENCY "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.in"
#                    DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}"
#                    COMMENT generating cxx/version.h )
#endif (WIN32)
#if (UNIX)
## on mac and linux we need the included replacement script
#ADD_CUSTOM_COMMAND(OUTPUT "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.h" 
#                    PRE_BUILD
#                    COMMAND "${PROJECT_SOURCE_DIR}/../tools/subwcrev.sh"
#                    ARGS "${PROJECT_SOURCE_DIR}"
#                         "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.in"
#                         "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.h"
#                    MAIN_DEPENDENCY "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.in"
#                    DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}"
#                    COMMENT generating cxx/version.h )
#endif (UNIX)

# Now publish the version file to public include dir
ADD_CUSTOM_COMMAND(OUTPUT "${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/version.h" 
                    PRE_BUILD
                    COMMAND "${CMAKE_COMMAND}" -E copy
                    ARGS "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.h"
                         "${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/"
                    MAIN_DEPENDENCY "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.h"
                    DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.h"
                    COMMENT publishing cxx/version.h )


set(iso54362xml_HEADER
        ${CMAKE_CURRENT_SOURCE_DIR}/c/data_point_c.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/c/iso5436_2_handle_c.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/c/messages_c.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/c/point_iterator_c.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/c/point_vector_c.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.h 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_lsb_point_vector_reader_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_lsb_point_vector_writer_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_msb_point_vector_reader_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_msb_point_vector_writer_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_point_vector_reader_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_point_vector_writer_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/data_point_impl.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/data_point_parser.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/double_data_point_parser.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/double_point_buffer.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/environment.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/float_data_point_parser.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/float_point_buffer.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/inline_validity.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/int16_data_point_parser.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/int16_point_buffer.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/int32_data_point_parser.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/int32_point_buffer.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/iso5436_2_container.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/missing_data_point_parser.hxx
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_buffer.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_validity_provider.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_iostream.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_parser.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_parser_builder.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_proxy.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_proxy_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_proxy_context_list.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_proxy_context_matrix.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_reader_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_writer_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/stdafx.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/valid_buffer.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/version.in 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/vector_buffer.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/vector_buffer_builder.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/win32_environment.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/linux_environment.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/xml_point_vector_reader_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/xml_point_vector_writer_context.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/zip_stream_buffer.hxx
		${CMAKE_CURRENT_SOURCE_DIR}/xyssl/md5.h		
		)
SET(opengps_HDRS
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/data_point.h 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/data_point_type.h 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/info.h 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/iso5436_2.h 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/messages.h 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/opengps.h 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/point_iterator.h 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/point_vector.h 
	)
SET(opengps_CXXHDRS
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/data_point.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/exceptions.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/info.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/iso5436_2.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/iso5436_2_handle.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/iso5436_2_xsd.hxx
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/opengps.hxx 
		 ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/point_iterator.hxx
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/point_vector.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/point_vector_base.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/string.hxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/version.h
		)
SET(iso54362xml_SRCS
        ${CMAKE_CURRENT_SOURCE_DIR}/xsd_Licence_Header.c 
        ${CMAKE_CURRENT_SOURCE_DIR}/c/data_point_c.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/c/iso5436_2_c.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/c/messages_c.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/c/point_iterator_c.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/c/point_vector_c.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_lsb_point_vector_reader_context.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_lsb_point_vector_writer_context.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_msb_point_vector_reader_context.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_msb_point_vector_writer_context.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_point_vector_reader_context.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/binary_point_vector_writer_context.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/data_point.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/data_point_impl.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/data_point_proxy.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/double_data_point_parser.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/double_point_buffer.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/environment.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/exceptions.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/float_data_point_parser.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/float_point_buffer.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/info.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/inline_validity.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/int16_data_point_parser.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/int16_point_buffer.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/int32_data_point_parser.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/int32_point_buffer.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/iso5436_2.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/iso5436_2_container.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/missing_data_point_parser.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_buffer.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_iterator.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_validity_provider.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_iostream.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_parser.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_parser_builder.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_proxy.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_proxy_context.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_proxy_context_list.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/point_vector_proxy_context_matrix.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/string.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/valid_buffer.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/vector_buffer.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/vector_buffer_builder.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/win32_environment.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/linux_environment.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/xml_point_vector_reader_context.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/xml_point_vector_writer_context.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/cxx/zip_stream_buffer.cxx
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include/opengps/cxx/iso5436_2_xsd.cxx
        ${CMAKE_CURRENT_SOURCE_DIR}/xyssl/md5.c
)

add_library( ${iso54362xmlNAME} ${LIBRARY_TYPE} ${iso54362xml_SRCS} ${iso54362xml_HEADER} ${opengps_HDRS} ${opengps_CXXHDRS})

target_link_libraries(${iso54362xmlNAME} ${ZLIB_LIBRARIES} ${MINIZIP_LIBRARIES} ${XERCESC_LIBRARY})

install(TARGETS  ${iso54362xmlNAME} 
       RUNTIME DESTINATION bin
	   LIBRARY DESTINATION lib
	   ARCHIVE DESTINATION lib
	)
install(FILES ${opengps_HDRS} DESTINATION include/opengps)	
install(FILES ${opengps_CXXHDRS} DESTINATION include/opengps/cxx)	

if (DEFINED MSVC_VERSION)
  set_target_properties(${iso54362xmlNAME}  PROPERTIES COMPILE_FLAGS "/Zc:wchar_t")
endif ()




SET(CPACK_PACKAGE_DESCRIPTION_SUMMARY "iso5436-2-xml")
SET(CPACK_PACKAGE_VENDOR "opengps")


SET(CPACK_PACKAGE_VERSION_MAJOR "1")
SET(CPACK_PACKAGE_VERSION_MINOR "0")
SET(CPACK_PACKAGE_VERSION_PATCH "0")


IF(WIN32 AND NOT UNIX)
SET(CPACK_GENERATOR ZIP)
ELSE(WIN32 AND NOT UNIX)

ENDIF(WIN32 AND NOT UNIX)


INCLUDE(CPack)

