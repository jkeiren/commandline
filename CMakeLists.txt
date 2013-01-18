cmake_minimum_required(VERSION 2.6)
project (cppcli)

if(NOT DEFINED CPPCLI_STANDALONE)
  option(CPPCLI_STANDALONE "Build CLI as standalone project" ON)
endif()

if(CPPCLI_STANDALONE)
  find_package(Boost)
  # Include external libraries
  add_subdirectory(external/googletest)

  set(CPPLOGGING_STANDALONE OFF)
  add_subdirectory(external/cpplogging)
endif()

message(STATUS "Using Boost headers from ${Boost_INCLUDE_DIR}")

# Trick to add header files to projects in IDEs like Visual Studio and XCode
FILE(GLOB_RECURSE HEADERS "include/**.h")
FILE(GLOB_RECURSE SOURCES "src/**.cpp")
add_custom_target(${PROJECT_NAME}_headers
  SOURCES ${HEADERS} ${SUB_HEADERS}
)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
set(CMAKE_CXX_FLAGS_ANALYSIS "${CMAKE_CXX_FLAGS} --analyse")

include_directories(
  include
  ${cpplogging_SOURCE_DIR}/include
  ${Boost_INCLUDE_DIR}
)

add_library (cppcli
  ${SOURCES}
)

target_link_libraries(cppcli
  cpplogging
)

# Define macro for build_and_run_test_targets
# This method compiles tests when invoked
macro(build_and_run_test_target TARGET)
  add_test(NAME "${TARGET}" COMMAND ${CMAKE_CTEST_COMMAND}
   --build-and-test
   "${CMAKE_CURRENT_SOURCE_DIR}"
   "${CMAKE_CURRENT_BINARY_DIR}"
   --build-noclean
   --build-nocmake
   --build-generator "${CMAKE_GENERATOR}"
   --build-makeprogram "${CMAKE_MAKE_PROGRAM}"
   --build-exe-dir "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}"
   --build-target "${TARGET}"
   --build-project "${PROJECT_NAME}"
   --test-command "${TARGET}"
  )
endmacro( build_and_run_test_target TARGET )

add_subdirectory(test)
