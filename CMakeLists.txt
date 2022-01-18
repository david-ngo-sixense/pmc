cmake_minimum_required(VERSION 3.10)
project(pmc VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 17)

# list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake") 

# Check build types
if (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message(STATUS "Setting build type to 'Release' as none was specified.")
    set(CMAKE_BUILD_TYPE "Release" CACHE
            STRING "Choose the type of build." FORCE)
endif ()


if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	# # Build Dependencies
	# add_subdirectory(cmake)
	# include_directories(${CMAKE_BINARY_DIR}/libunistd-src/unistd)
	# # link_directories("C:/dev/libunistd/build/Release")
	# set(LIBUNISTD_LIB ${CMAKE_BINARY_DIR}/libunistd-build/Release/libunistd.lib)
	
	# libunistd  
configure_file(cmake/libunistd.CMakeLists.txt.in libunistd-download/CMakeLists.txt)
execute_process(COMMAND "${CMAKE_COMMAND}" -G "${CMAKE_GENERATOR}" .
        WORKING_DIRECTORY "${CMAKE_BINARY_DIR}/libunistd-download")
execute_process(COMMAND "${CMAKE_COMMAND}" --build .
        WORKING_DIRECTORY "${CMAKE_BINARY_DIR}/libunistd-download")
add_subdirectory("${CMAKE_BINARY_DIR}/libunistd-src"
        "${CMAKE_BINARY_DIR}/libunistd-build")

#include_directories(${CMAKE_BINARY_DIR}/libunistd-src/unistd)
#set(LIBUNISTD_LIB ${CMAKE_BINARY_DIR}/libunistd-build/Release/libunistd.lib)

 
endif()
	
# Building Targets
set(PMC_ROOT ${CMAKE_CURRENT_LIST_DIR})
add_subdirectory(pmc)





# export targets
export(TARGETS ${PMC_EXPORTED_TARGETS} FILE pmc-exports.cmake)
install(FILES cmake/pmcConfig.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/pmc
        )

 

 
message("PMC_EXPORTED_TARGETS:  ${PMC_EXPORTED_TARGETS}")
		
# install(TARGETS pmc
        # EXPORT CMakeExternalTargets
        # LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        # ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        # )
# install(TARGETS pmc_main
        # EXPORT CMakeExternalTargets
        # LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        # ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        # )
# # Install
# install(EXPORT CMakeExternalTargets
	# NAMESPACE CMakeExternal::
	# DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/CMakeExternal
	# COMPONENT Devel)
	
 