### Required
project(libmodman CXX)
set_project_version(1 0 1)

### Main library
# Flags / Definitions / Environment
if (WIN32)
  add_definitions(-D_CRT_SECURE_NO_WARNINGS=1)
else()
#  add_definitions(-D_POSIX_C_SOURCE=1)
  set(CMAKE_CXX_FLAGS "-g -Wall -Werror -fvisibility=hidden -Os ${CMAKE_CXX_FLAGS}")
endif()
include_directories(${CMAKE_SOURCE_DIR})

# PkgConfig file
if(NOT WIN32 AND NOT APPLE)
  configure_file(${CMAKE_CURRENT_SOURCE_DIR}/libmodman-1.0.pc.in
                 ${CMAKE_CURRENT_BINARY_DIR}/libmodman-1.0.pc @ONLY)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libmodman-1.0.pc
          DESTINATION ${LIB_INSTALL_DIR}/pkgconfig)
endif()

# CMake Find helper
if (NOT WIN32 AND NOT APPLE)
  configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Findlibmodman.cmake.in
                 ${CMAKE_CURRENT_BINARY_DIR}/Findlibmodman.cmake @ONLY)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/Findlibmodman.cmake
          DESTINATION ${SHARE_INSTALL_PREFIX}/cmake/Modules)
endif()

# Define the library itself
add_library(libmodman SHARED
		${CMAKE_CURRENT_SOURCE_DIR}/module.hpp
		${CMAKE_CURRENT_SOURCE_DIR}/module_manager.hpp
		${CMAKE_CURRENT_SOURCE_DIR}/module_manager.cpp)
if(NOT WIN32)
  target_link_libraries(libmodman dl)
endif()
set_target_properties(libmodman PROPERTIES PREFIX "" VERSION 0.0.0 SOVERSION 0)
install(TARGETS libmodman                     DESTINATION ${LIB_INSTALL_DIR})
install(FILES   module_manager.hpp module.hpp DESTINATION ${INCLUDE_INSTALL_DIR}/${PROJECT_NAME})

### Tests
add_testdirectory(test)
