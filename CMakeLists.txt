cmake_minimum_required(VERSION 3.10)
project(pmc VERSION 1.0.0)
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

# Apple: Don't modify install_name when touching RPATH.
if(POLICY CMP0068)
	cmake_policy(SET CMP0068 NEW)
endif()

# Force default build type to Release
set(CMAKE_BUILD_TYPE "Release" CACHE STRING
	"Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel.")
	
# include_directories("C:/dev/libunistd/unistd")
# link_directories("C:/dev/libunistd/build/Release")
# set(LIBUNISTD_LIB "C:/dev/libunistd/build/Release/libunistd.lib")

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

target_include_directories(pmc PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
        )

add_executable(pmc_main pmc_driver.cpp)
# target_link_libraries(pmc_main pmc ${LIBUNISTD_LIB})
target_link_libraries(pmc_main pmc)

find_package(OpenMP REQUIRED)
# target_link_libraries(pmc OpenMP::OpenMP_CXX ${LIBUNISTD_LIB})
target_link_libraries(pmc OpenMP::OpenMP_CXX)
target_link_libraries(pmc_main OpenMP::OpenMP_CXX)

# Installation
include(GNUInstallDirs)

install(TARGETS pmc
        EXPORT pmc-export
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        )

export(
        EXPORT pmc-export
        FILE ${CMAKE_CURRENT_BINARY_DIR}/pmcTargets.cmake
        NAMESPACE pmc::
)

install(EXPORT pmc-export
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/pmc
        NAMESPACE pmc::
        FILE pmcTargets.cmake
        )

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(FILES cmake/pmcConfig.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/pmc
        )

# Build Dependencies
add_subdirectory(cmake)

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
include(CMakePackageConfigHelpers)
configure_package_config_file(cmake/CMakeExternalConfig.cmake.in
	"${PROJECT_BINARY_DIR}/CMakeExternalConfig.cmake"
	INSTALL_DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/CMakeExternal"
	NO_SET_AND_CHECK_MACRO
  NO_CHECK_REQUIRED_COMPONENTS_MACRO)
write_basic_package_version_file(
	"${PROJECT_BINARY_DIR}/CMakeExternalConfigVersion.cmake"
	COMPATIBILITY SameMajorVersion)
install(
	FILES
	"${PROJECT_BINARY_DIR}/CMakeExternalConfig.cmake"
	"${PROJECT_BINARY_DIR}/CMakeExternalConfigVersion.cmake"
	DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/CMakeExternal"
	COMPONENT Devel)
	
include_directories(${CMAKE_BINARY_DIR}/libunistd-src/unistd)
# link_directories("C:/dev/libunistd/build/Release")
set(LIBUNISTD_LIB ${CMAKE_BINARY_DIR}/libunistd-build/Release/libunistd.lib)
target_link_libraries(pmc PUBLIC ${LIBUNISTD_LIB})
target_link_libraries(pmc_main PUBLIC ${LIBUNISTD_LIB})