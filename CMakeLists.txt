cmake_minimum_required(VERSION 3.10)
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake") 

# Apple: Don't modify install_name when touching RPATH.
if(POLICY CMP0068)
	cmake_policy(SET CMP0068 NEW)
endif()

# Force default build type to Release
set(CMAKE_BUILD_TYPE "Release" CACHE STRING
	"Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel.")

project(pmc VERSION 1.0.0)

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

include_directories(${CMAKE_BINARY_DIR}/libunistd-src/unistd)
set(LIBUNISTD_LIB ${CMAKE_BINARY_DIR}/libunistd-build/Release/libunistd.lib)
endif()
	
set(PMC_SOURCE_FILES
        ${CMAKE_CURRENT_SOURCE_DIR}/pmc_heu.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pmc_maxclique.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pmcx_maxclique.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pmcx_maxclique_basic.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pmc_cores.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pmc_utils.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pmc_graph.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pmc_clique_utils.cpp
        )
 

add_library(pmc SHARED ${PMC_SOURCE_FILES})

if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	target_link_libraries(pmc PUBLIC ${LIBUNISTD_LIB})
	add_dependencies(pmc libunistd) # very important. If not, pmc is compiled before libunistd is compiled
endif()

 


add_executable(pmc_main pmc_driver.cpp)
# target_link_libraries(pmc_main pmc ${LIBUNISTD_LIB})
target_link_libraries(pmc_main PUBLIC pmc)

if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	target_link_libraries(pmc_main PUBLIC ${LIBUNISTD_LIB})
endif()

find_package(OpenMP REQUIRED)
# target_link_libraries(pmc OpenMP::OpenMP_CXX ${LIBUNISTD_LIB})
target_link_libraries(pmc PUBLIC OpenMP::OpenMP_CXX)
target_link_libraries(pmc_main PUBLIC OpenMP::OpenMP_CXX)





# Installation
include(GNUInstallDirs)
 
target_include_directories(pmc PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
        )
		
install(TARGETS pmc
        EXPORT pmc-export
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
		RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin
        )
	 
list(APPEND PMC_EXPORTED_TARGETS pmc)
if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	list(APPEND PMC_EXPORTED_TARGETS libportable libunistd liblmdb libregex libsqlite libuuid libxxhash)
endif()


install(EXPORT pmc-export
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/pmc
        NAMESPACE pmc::
        FILE pmcTargets.cmake
        )
		

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

set(PMC_EXPORTED_TARGETS "${PMC_EXPORTED_TARGETS}")

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
	
 