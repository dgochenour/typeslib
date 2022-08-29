cmake_minimum_required(VERSION 3.1)

project(typelib CXX)

# Set the platform here
set(TARGET_ARCH x64Linux4gcc7.3.0)

link_directories($ENV{NDDSHOME}/lib/${TARGET_ARCH})
set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -m64 -Wall")

# Figure out if we are using shared or static libs
if(DEFINED RTI_SHARED_LIB)
    set(SHAREDLIB_SFX "")
else()
    set(SHAREDLIB_SFX "z")
    add_compile_definitions(RTI_STATIC)
endif()
if(${CMAKE_BUILD_TYPE} MATCHES "[Rr]elease")
    set(DEBUG_SFX "")
else()
    set(DEBUG_SFX "d")
    set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -g -O0")
    set(CMAKE_EXE_LINKER_FLAGS  "${CMAKE_EXE_LINKER_FLAGS} -g")
endif()

add_compile_definitions(RTI_UNIX RTI_LINUX RTI_64BIT)

set(SYSLIBS dl m pthread rt)
set(CONNEXT_PRO_LIBS 
    nddscpp2${SHAREDLIB_SFX}${DEBUG_SFX} 
    nddsc${SHAREDLIB_SFX}${DEBUG_SFX} 
    nddscore${SHAREDLIB_SFX}${DEBUG_SFX}
)
set(GEN_DIR gen)
set(IDL_DIR idl)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}
    $ENV{NDDSHOME}/include 
    $ENV{NDDSHOME}/include/ndds
    $ENV{NDDSHOME}/include/ndds/hpp     
)


file(GLOB IDL_SOURCE_FILES "${IDL_DIR}/*.idl")

foreach(idl ${IDL_SOURCE_FILES})
    message(${idl})
    get_filename_component(filename ${idl} NAME)
    string(REGEX REPLACE "\\.idl" "" basename ${filename})
    set(THIS_PASS_CXX 
        ${CMAKE_CURRENT_SOURCE_DIR}/${GEN_DIR}/${basename}.cxx 
        ${CMAKE_CURRENT_SOURCE_DIR}/${GEN_DIR}/${basename}Plugin.cxx
    )                   
    set(THIS_PASS_HPP 
        ${CMAKE_CURRENT_SOURCE_DIR}/${GEN_DIR}/${basename}.hpp
        ${CMAKE_CURRENT_SOURCE_DIR}/${GEN_DIR}/${basename}Plugin.hpp)
    list(APPEND GENERATED_CXX ${THIS_PASS_CXX})
    list(APPEND GENERATED_HPP ${THIS_PASS_HPP})

    add_custom_command(
        OUTPUT ${THIS_PASS_CXX} ${THIS_PASS_HPP}
        COMMAND $ENV{NDDSHOME}/bin/rtiddsgen -language C++11 -update typefiles -d ${CMAKE_CURRENT_SOURCE_DIR}/${GEN_DIR} ${idl}
        COMMENT "Generation type support plugin from ${idl}"
    )
endforeach()

add_library(typelib STATIC ${GENERATED_CXX} ${GENERATED_HPP})
target_link_libraries(typelib ${CONNEXT_PRO_LIBS} ${SYSLIBS})