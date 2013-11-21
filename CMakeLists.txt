cmake_minimum_required(VERSION 2.6)
project (cppcli)

# Target directories for the installation
if(NOT DEFINED ${PROJECT_NAME}_BIN_DIR)
  set(${PROJECT_NAME}_BIN_DIR "bin" CACHE FILEPATH "Installation directory for the binaries")
endif()
if(NOT DEFINED ${PROJECT_NAME}_LIB_DIR)
  set(${PROJECT_NAME}_LIB_DIR "lib" CACHE FILEPATH "Installation directory for the libraries")
endif()
if(NOT DEFINED ${PROJECT_NAME}_INCLUDE_DIR)
  set(${PROJECT_NAME}_INCLUDE_DIR "include/${PROJECT_NAME}" CACHE FILEPATH "Installation directory for the headers")
endif()

# Google test
if(NOT DEFINED ${PROJECT_NAME}_USE_INTERNAL_GOOGLETEST)
  set(${PROJECT_NAME}_USE_INTERNAL_GOOGLETEST ON CACHE BOOL "If set, the internal version of googletest is used, otherwise we rely on a version of googletest being included in a calling script")
endif()

# Logging library
if(NOT DEFINED ${PROJECT_NAME}_USE_INTERNAL_CPPLOGGING)
  set(${PROJECT_NAME}_USE_INTERNAL_CPPLOGGING ON CACHE BOOL "If set, the internal version of cpplogging is used.")
endif()

### Include directories for external projects, if required
if(${PROJECT_NAME}_USE_INTERNAL_GOOGLETEST)
  message(STATUS "${PROJECT_NAME}: Using Google test from external/googletest")
  add_subdirectory(external/googletest)
endif()

if(${PROJECT_NAME}_USE_INTERNAL_CPPLOGGING)
  message(STATUS "${PROJECT_NAME}: Using cpplogging from external/cpplogging")
  set(cpplogging_USE_INTERNAL_GOOGLETEST OFF)
  add_subdirectory(external/cpplogging)
else()
  set(cpplogging_LIB_DIR "" CACHE PATH "cpplogging library path")
  set(cpplogging_INCLUDE_DIR "" CACHE PATH "cpplogging header path")
endif()

### If boost was not already included elsewhere, get it here
if(NOT Boost_INCLUDE_DIR)
  find_package(Boost)
  message(STATUS "Using Boost headers from ${Boost_INCLUDE_DIR}")
endif()

### Trick to add header files to projects in IDEs like Visual Studio and XCode
file(GLOB_RECURSE ${PROJECT_NAME}_HEADERS "include/**.h")
file(GLOB_RECURSE ${PROJECT_NAME}_SOURCES "src/**.cpp")

add_custom_target(${PROJECT_NAME}_headers
  SOURCES ${${PROJECT_NAME}_HEADERS}
)

# C++
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
set(CMAKE_CXX_FLAGS_ANALYSIS "${CMAKE_CXX_FLAGS} --analyse")

include_directories(
  include
  ${cpplogging_INCLUDE_DIR}
  ${Boost_INCLUDE_DIR}
)

add_library (${PROJECT_NAME}
  ${${PROJECT_NAME}_SOURCES}
)

target_link_libraries(${PROJECT_NAME}
  cpplogging
)

install (TARGETS ${PROJECT_NAME} DESTINATION ${${PROJECT_NAME}_LIB_DIR})
install (FILES ${HEADERS} DESTINATION ${${PROJECT_NAME}_INCLUDE_DIR})
