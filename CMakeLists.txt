cmake_minimum_required (VERSION 2.8)

project(Message)

find_package(Protobuf REQUIRED)
include_directories(${PROTOBUF_INCLUDE_DIRS})

###########################################
#       Compilation flags
###########################################
set( STATIC_BUILD false )


set(CMAKE_CXX_FLAGS "-std=c++11 -fPIC -Wall -Wextra -Wno-missing-field-initializers ${CMAKE_CXX_FLAGS}")
set(CMAKE_C_FLAGS "-std=c99 -fPIC -Wall -Wextra -Wno-missing-field-initializers ${CMAKE_C_FLAGS}")


# RPATH handling
if(APPLE)
    set(CMAKE_MACOSX_RPATH ON)
    set(CMAKE_SKIP_BUILD_RPATH FALSE)
    set(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
    set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")
    set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
    list(FIND CMAKE_PLATFORM_IMPLICIT_LINK_DIRECTORIES "${CMAKE_INSTALL_PREFIX}/lib" isSystemDir)
    if("${isSystemDir}" STREQUAL "-1")
        set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")
    endif()
endif()

add_definitions(-D_FILE_OFFSET_BITS=64)

###########################################
#       Add gtest
###########################################

# if(NOT WIN_ENV)
#       set (GTEST_COMPILE_DIR ${CMAKE_BINARY_DIR}/bin/gtest)
#       add_subdirectory(dependency/gtest ${GTEST_COMPILE_DIR})
#       link_directories(${GTEST_COMPILE_DIR})
#       include_directories(dependency/gtest/googletest/include)
#       enable_testing()
#       add_custom_target(check
#           COMMAND find . -name "*.gcda" -type f -delete
#           COMMAND ${CMAKE_CTEST_COMMAND} -V -T Test )
#       add_subdirectory(test/unitTest)
# endif()

###########################################
#       Configure destination folders
###########################################

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_INSTALL_PREFIX "bin")


###########################################
#       Default options
###########################################

if(STATIC_BUILD)
    option(BUILD_SHARED_LIBS "Build shared libraries (DLLs)." OFF)
else()
    option(BUILD_SHARED_LIBS "Build shared libraries (DLLs)." ON)
endif()

###########################################
#       Mavlink
###########################################
# if(NOT WIN_ENV)
#     add_custom_target(
#         mavlink_stuff ALL
#         COMMAND cd ${CMAKE_SOURCE_DIR}/dependency/mavlink/v1.0 && make)
# else()
#     add_custom_target(
#         mavlink_stuff ALL
#         COMMAND cd ${CMAKE_SOURCE_DIR}/dependency/mavlink/v1.0 && lily_generate_msgs.bat)
# endif()

###########################################
#       Sub-directories
###########################################

PROTOBUF_GENERATE_CPP(PROTO_SRCS PROTO_HDRS msg/Message.proto)
add_executable(msg main.cpp ${PROTO_SRCS} ${PROTO_HDRS})
target_link_libraries(msg ${PROTOBUF_LIBRARIES})
