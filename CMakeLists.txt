# Project's name
PROJECT ( init C CXX )

# Specify the minimum version for CMake

cmake_minimum_required(VERSION 3.1)

add_definitions(-std=c++11)

# Set the output folder where your program will be created
set(CMAKE_BINARY_DIR ${CMAKE_SOURCE_DIR}/bin)
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/lib)
set(THREADS_PREFER_PTHREAD_FLAG ON)

find_package (Threads REQUIRED)
find_package(SDL2 REQUIRED)

# The following folder will be included
include_directories("${PROJECT_SOURCE_DIR}/include")

add_executable(main ${PROJECT_SOURCE_DIR}/main.cpp)

if (${CMAKE_LIBRARY_ARCHITECTURE} STREQUAL "arm-linux-gnueabihf") # Jetson TK1 / Pi
    set(PHOENIX_LIBS_DIR ${CMAKE_SOURCE_DIR}/lib/raspberry)
elseif (${CMAKE_LIBRARY_ARCHITECTURE} STREQUAL "aarch64-linux-gnu") # Jetson TX2
    set(PHOENIX_LIBS_DIR ${CMAKE_SOURCE_DIR}/lib/jetsontx)
else()
    set(PHOENIX_LIBS_DIR ${CMAKE_SOURCE_DIR}/lib/x86-64)
endif()

target_link_libraries(main ${PHOENIX_LIBS_DIR}/libCTRE_Phoenix.so)
target_link_libraries(main ${PHOENIX_LIBS_DIR}/libCTRE_PhoenixCCI.so)

target_link_libraries(main Threads::Threads)
target_link_libraries(main ${SDL2_LIBRARIES})

find_library(WIRINGPI_LIBRARIES NAMES wiringPi)
target_link_libraries(main ${WIRINGPI_LIBRARIES})

add_custom_command(TARGET main POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${PHOENIX_LIBS_DIR}
        $<TARGET_FILE_DIR:main>)

# add_executable(main ${CMAKE_SOURCE_DIR}/main.cpp)

# find_library(WIRINGPI_LIBRARIES NAMES wiringPi)
# target_link_libraries(main ${WIRINGPI_LIBRARIES})

# target_link_libraries(main ${CMAKE_SOURCE_DIR}/lib/raspberry/libCTRE_Phoenix.a)
# target_link_libraries(main ${CMAKE_SOURCE_DIR}/lib/raspberry/libCTRE_PhoenixCCI.a)
# target_link_libraries(main ${CMAKE_SOURCE_DIR}/lib/raspberry/libCTRE_PhoenixDiagnostics.a)
# target_link_libraries(main ${CMAKE_SOURCE_DIR}/lib/raspberry/libCTRE_PhoenixPlatform_socketcan.a)
# target_link_libraries(main ${CMAKE_SOURCE_DIR}/lib/raspberry/libCTRE_PhoenixCanutils.a)
# target_link_libraries(main ${CMAKE_SOURCE_DIR}/lib/raspberry/libCTRE_PhoenixCore.a)

# target_link_libraries(main Threads::Threads)
# target_link_libraries(main ${SDL2_LIBRARIES})
