project(pmc_source)
include(GNUInstallDirs)

# set(LIBUNISTD_LIB "C:/dev/libunistd/build/Release/libunistd.lib")
 set(PMC_SOURCE_FILES
        src/pmc_heu.cpp
        src/pmc_maxclique.cpp
        src/pmcx_maxclique.cpp
        src/pmcx_maxclique_basic.cpp
        src/pmc_cores.cpp
        src/pmc_utils.cpp
        src/pmc_graph.cpp
        src/pmc_clique_utils.cpp
        )
 

add_library(pmc SHARED ${PMC_SOURCE_FILES})
if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	target_link_libraries(pmc PUBLIC libunistd)
endif()


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
 
if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	
	# add_dependencies(pmc libunistd) # very important. If not, pmc is compiled before libunistd is compiled
	list(APPEND PMC_EXPORTED_TARGETS pmc libportable libunistd)
else()
	list(APPEND PMC_EXPORTED_TARGETS pmc)
endif()
add_library(pmc::pmc ALIAS pmc)
 


# add_executable(pmc_main src/pmc_driver.cpp)
# # target_link_libraries(pmc_main pmc ${LIBUNISTD_LIB})
# target_link_libraries(pmc_main PUBLIC pmc)

# if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	# target_link_libraries(pmc_main PUBLIC libunistd)
# endif()

find_package(OpenMP REQUIRED)
# target_link_libraries(pmc OpenMP::OpenMP_CXX ${LIBUNISTD_LIB})
target_link_libraries(pmc PUBLIC OpenMP::OpenMP_CXX)
# target_link_libraries(pmc_main PUBLIC OpenMP::OpenMP_CXX)


 
 

	 

 

# set exported targets in parent scope
set(PMC_EXPORTED_TARGETS "${PMC_EXPORTED_TARGETS}" PARENT_SCOPE)

install(EXPORT pmc-export
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/pmc
        NAMESPACE pmc::
        FILE pmcTargets.cmake
        )
		

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

 