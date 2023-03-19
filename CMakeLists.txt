cmake_minimum_required(VERSION 3.16.3)
project(CGAA_CMAKE)

set(CMAKE_CXX_STANDARD 17)

include_directories(${PROJECT_SOURCE_DIR}/include)
aux_source_directory(src DIR_SRCS)

# Add directory with macros
set (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake)

# GLM library is required
find_package (GLM REQUIRED)
if (GLM_FOUND)
    include_directories (${GLM_INCLUDE_DIR})
else (GLM_FOUND)
    message (SEND_ERROR "Unable to locate GLM")
endif (GLM_FOUND)

# link source code
add_executable(CGAA_CMAKE main.cpp ${DIR_SRCS})

# Find GLFW3
find_package(GLFW3 REQUIRED)
message(STATUS "Found GLFW3 in ${GLFW3_INCLUDE_DIR}")
if(GLFW3_FOUND)
    include_directories(${GLFW3_INCLUDE_DIR})
    target_link_libraries(${PROJECT_NAME} ${GLFW3_LIBRARY})
endif()

if (WIN32)
    # copy dll to .exe folder
    add_custom_target(DllCopy ALL
            COMMAND ${CMAKE_COMMAND} -E copy_directory
            "${PROJECT_SOURCE_DIR}/thirdparty/bin/"
            "${CMAKE_BINARY_DIR}/$<CONFIG>/"
            WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
            VERBATIM)
endif ()
