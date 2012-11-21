cmake_minimum_required(VERSION 2.6)
project (commandline)

# Include external libraries
#add_subdirectory(external/googletest)
#add_subdirectory(external/logging)

# Trick to add header files to projects in IDEs like Visual Studio and XCode
FILE(GLOB HEADERS "include/**.h")
FILE(GLOB SOURCES "src/**.cpp")
add_custom_target(${PROJECT_NAME}_headers
  SOURCES ${HEADERS} ${SUB_HEADERS}
)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
set(CMAKE_CXX_FLAGS_ANALYSIS "${CMAKE_CXX_FLAGS} --analyse")

include_directories(include)

add_library (cppcli
  ${SOURCES}
)
