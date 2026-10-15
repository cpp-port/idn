cmake_minimum_required(VERSION 3.16)
project(idn C)

if(__REACTOS__)
    file(GENERATE OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/config.h" CONTENT "#pragma once\n#include <stddef.h>\n#include <stdint.h>\n#define _(text) (text)\nchar *str_iconv(const char *, const char *, const char *);\n#define HAVE_ICONV 1\n#define ICONV_CONST\n#define HAVE_STDINT_H 1\n#define HAVE_STRING_H 1\n#define HAVE_STDLIB_H 1\n#define HAVE_LOCALE_H 1\n#define c_strcasecmp _stricmp\n#define c_strncasecmp _strnicmp\n#define _GL_ATTRIBUTE_PURE\n#define _GL_ATTRIBUTE_CONST\n")
    file(GENERATE OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/configmake.h" CONTENT "#define LIBDIR \".\"\n")
    set(idn_sources idn-free.c idna.c nfkc.c pr29.c profiles.c punycode.c rfc3454.c
     strerror-idna.c strerror-pr29.c strerror-punycode.c strerror-stringprep.c
     strerror-tld.c stringprep.c tld.c tlds.c toutf8.c gl/striconv.c)
    list(TRANSFORM idn_sources PREPEND "${CMAKE_CURRENT_SOURCE_DIR}/lib/")
    add_library(idn SHARED ${idn_sources})
    target_compile_definitions(idn PRIVATE HAVE_CONFIG_H LIBIDN_BUILDING WINDOWS)
    target_include_directories(idn PRIVATE "${CMAKE_CURRENT_BINARY_DIR}" "${WORKSPACE_FOLDER}/operating_system/operating_system-windows" "${CMAKE_CURRENT_SOURCE_DIR}/include" "${CMAKE_CURRENT_SOURCE_DIR}/lib" "${CMAKE_CURRENT_SOURCE_DIR}/lib/gl" "${CMAKE_CURRENT_SOURCE_DIR}/../iconv/include")
    target_link_libraries(idn PRIVATE iconv)
    set_target_properties(idn PROPERTIES PREFIX "" WINDOWS_EXPORT_ALL_SYMBOLS ON)
    if(MINGW)
        target_link_options(idn PRIVATE -static-libgcc)
    endif()
    install(TARGETS idn RUNTIME DESTINATION binary ARCHIVE DESTINATION library)
    install(DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/include/idn" DESTINATION include)
    install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/lib/idn-free.h" "${CMAKE_CURRENT_SOURCE_DIR}/lib/stringprep.h" "${CMAKE_CURRENT_SOURCE_DIR}/lib/punycode.h" DESTINATION include)

    return()
endif()

#set(VERSION "1.2.11")

#option(ASM686 "Enable building i686 assembly implementation")
#option(AMD64 "Enable building amd64 assembly implementation")

#set(INSTALL_BIN_DIR "${CMAKE_INSTALL_PREFIX}/bin" CACHE PATH "Installation directory for executables")
#set(INSTALL_LIB_DIR "${CMAKE_INSTALL_PREFIX}/lib" CACHE PATH "Installation directory for libraries")
#set(INSTALL_INC_DIR "${CMAKE_INSTALL_PREFIX}/include" CACHE PATH "Installation directory for headers")
#set(INSTALL_MAN_DIR "${CMAKE_INSTALL_PREFIX}/share/man" CACHE PATH "Installation directory for manual pages")
#set(INSTALL_PKGCONFIG_DIR "${CMAKE_INSTALL_PREFIX}/share/pkgconfig" CACHE PATH "Installation directory for pkgconfig (.pc) files")

#include(CheckTypeSize)
#include(CheckFunctionExists)
#include(CheckIncludeFile)
#include(CheckCSourceCompiles)
#enable_testing()

if(MSVC)
    #set(CMAKE_DEBUG_POSTFIX "d")
    add_definitions(-D_CRT_SECURE_NO_DEPRECATE)
    add_definitions(-D_CRT_NONSTDC_NO_DEPRECATE)
    include_directories(${CMAKE_CURRENT_SOURCE_DIR})
endif()


#set(ZLIB_PC ${CMAKE_CURRENT_BINARY_DIR}/zlib.pc)
#configure_file( ${CMAKE_CURRENT_SOURCE_DIR}/zlib.pc.cmakein
#		${ZLIB_PC} @ONLY)
#configure_file(	${CMAKE_CURRENT_SOURCE_DIR}/zconf.h.cmakein
#		${CMAKE_CURRENT_BINARY_DIR}/zconf.h @ONLY)
#include_directories(${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_SOURCE_DIR})


#set(IDN_PUBLIC_HDRS
 #   ${PROJECT_NAME}/idna.h
#)

#target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/../..)
#target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/..)



set(IDN_PRIVATE_HDRS
        #config.h
        lib/tld.h
)

set(IDN_SRCS
        lib/${PROJECT_NAME}-free.c
        lib/idna.c
        lib/nfkc.c
        lib/pr29.c
        lib/profiles.c
        lib/punycode.c
        lib/rfc3454.c
        lib/strerror-idna.c
        lib/strerror-pr29.c
        lib/strerror-punycode.c
        lib/strerror-stringprep.c
        lib/strerror-tld.c
        lib/stringprep.c
        lib/tld.c
        lib/tlds.c
        lib/toutf8.c
        #lib/version.c
        lib/gl/striconv.c
)


add_library(${PROJECT_NAME} SHARED ${IDN_SRCS} ${IDN_PUBLIC_HDRS} ${IDN_PRIVATE_HDRS})
add_library(static_${PROJECT_NAME} STATIC ${IDN_SRCS} ${IDN_PUBLIC_HDRS} ${IDN_PRIVATE_HDRS})


target_compile_definitions(${PROJECT_NAME} PRIVATE HAVE_CONFIG_H LIBIDN_BUILDING)
target_compile_definitions(static_${PROJECT_NAME} PRIVATE HAVE_CONFIG_H LIBIDN_BUILDING LIBIDN_STATIC)

target_link_libraries(${PROJECT_NAME} PRIVATE iconv)

set_target_properties(${PROJECT_NAME} PROPERTIES DEFINE_SYMBOL IDN_DLL)


list(APPEND library_include_directories ${CMAKE_CURRENT_SOURCE_DIR})
list(APPEND library_include_directories ${CMAKE_CURRENT_SOURCE_DIR}/include/${PROJECT_NAME})
list(APPEND library_include_directories ${CMAKE_CURRENT_SOURCE_DIR}/../../../operating_system/operating_system-${PLATFORM_NAME}/configuration/${PROJECT_NAME})
list(APPEND library_include_directories ${CMAKE_CURRENT_SOURCE_DIR}/../../../operating_system/operating_system-${PLATFORM_NAME}/include)


target_include_directories(${PROJECT_NAME} PRIVATE ${library_include_directories})
target_include_directories(static_${PROJECT_NAME} PRIVATE ${library_include_directories})

