set(BUILD_SHARED_LIBS OFF)
set(BUILD_TESTING OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
 

############
##  LIBUNISTD  ##
############
message(STATUS "Subproject: LIBUNISTD...")
# Download and unpack libunistd at configure time
configure_file(
	${CMAKE_CURRENT_SOURCE_DIR}/libunistd.CMakeLists.txt
	${CMAKE_BINARY_DIR}/libunistd-download/CMakeLists.txt)
execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/libunistd-download)
if(result)
  message(FATAL_ERROR "CMake step for libunistd failed: ${result}")
endif()
execute_process(COMMAND ${CMAKE_COMMAND} --build .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/libunistd-download)
if(result)
  message(FATAL_ERROR "Build step for libunistd failed: ${result}")
endif()

add_subdirectory(
	${CMAKE_BINARY_DIR}/libunistd-src
	${CMAKE_BINARY_DIR}/libunistd-build)
message(STATUS "Subproject: LIBUNISTD...DONE")
 