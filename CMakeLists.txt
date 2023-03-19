cmake_minimum_required(VERSION 3.16.3)
project(Jyamithika_CMAKE)

set(CMAKE_CXX_STANDARD 17)

include_directories(${PROJECT_SOURCE_DIR}/include)
aux_source_directory(src DIR_SRCS)
aux_source_directory(include/Jyamithika DIR_SRCS01)
aux_source_directory(include/Jyamithika/Trees DIR_SRCS02)
aux_source_directory(include/Jyamithika/Core DIR_SRCS03)
aux_source_directory(include/Jyamithika/Core/Base DIR_SRCS04)
aux_source_directory(include/Jyamithika/Core/Primitives DIR_SRCS05)
aux_source_directory(include/GraphicUtils DIR_SRCS06)
aux_source_directory(include/GraphicUtils/Imgui DIR_SRCS07)

# Add directory with macros
set (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake)

# GLM library is required
find_package (GLM REQUIRED)
if (GLM_FOUND)
    include_directories (${GLM_INCLUDE_DIR})
else (GLM_FOUND)
    message (SEND_ERROR "Unable to locate GLM")
endif (GLM_FOUND)

# Find all example .cpp
file(
        GLOB_RECURSE mains RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}/example"
        "${CMAKE_CURRENT_SOURCE_DIR}/example/BSPVisualization.cpp"
        "${CMAKE_CURRENT_SOURCE_DIR}/example/ConstructingConvexhull_2d_simulation.cpp"
        "${CMAKE_CURRENT_SOURCE_DIR}/example/Convexhull3dSimulation.cpp"
        "${CMAKE_CURRENT_SOURCE_DIR}/example/KDTree2DSimulation.cpp"
        "${CMAKE_CURRENT_SOURCE_DIR}/example/PolygonTriangulationSimulation.cpp"
        "${CMAKE_CURRENT_SOURCE_DIR}/example/QuadTreeVisualization.cpp"
        "${CMAKE_CURRENT_SOURCE_DIR}/example/SimpleVoronoi2dSimulation.cpp"
)

# Find GLFW3
find_package(GLFW3 REQUIRED)
message(STATUS "Found GLFW3 in ${GLFW3_INCLUDE_DIR}")
if(GLFW3_FOUND)
    include_directories(${GLFW3_INCLUDE_DIR})
endif()

# link source code
foreach(mainfile IN LISTS mains)
    # Get file name without directory
    get_filename_component(mainname ${mainfile} NAME_WE)
    add_executable(${mainname} ${CMAKE_CURRENT_SOURCE_DIR}/example/${mainfile} ${DIR_SRCS} ${DIR_SRCS01}
            ${DIR_SRCS02} ${DIR_SRCS03} ${DIR_SRCS04} ${DIR_SRCS05} ${DIR_SRCS06} ${DIR_SRCS07})
    if(GLFW3_FOUND)
        target_link_libraries(${mainname} ${GLFW3_LIBRARY})
    endif()
endforeach()

if (WIN32)
    # copy dll to .exe folder
    add_custom_target(DllCopy ALL
            COMMAND ${CMAKE_COMMAND} -E copy_directory
            "${PROJECT_SOURCE_DIR}/thirdparty/bin/"
            "${CMAKE_BINARY_DIR}/$<CONFIG>/"
            WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
            VERBATIM)
endif ()

set(CMAKE_EXE_LINKER_FLAGS /NODEFAULTLIB:"MSVCRT.lib")
