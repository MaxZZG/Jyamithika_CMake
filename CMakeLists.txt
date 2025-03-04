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

include(FetchContent)
# GLM library is required
FetchContent_Declare(
    glm
    GIT_REPOSITORY https://github.com/g-truc/glm.git
    GIT_SHALLOW TRUE
)
FetchContent_MakeAvailable(glm)
list(APPEND INCLUDES ${glm_SOURCE_DIR}/glm)

# glfw
FetchContent_Declare(
        glfw
        GIT_REPOSITORY https://github.com/glfw/glfw.git
        GIT_SHALLOW TRUE
)

FetchContent_MakeAvailable(glfw)
list(APPEND LIBS glfw)
list(APPEND INCLUDES ${glfw_SOURCE_DIR}/include)

message(STATUS "INCLUDES: ${INCLUDES}")

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

# link source code
foreach(mainfile IN LISTS mains)
    # Get file name without directory
    get_filename_component(mainname ${mainfile} NAME_WE)
    add_executable(${mainname} ${CMAKE_CURRENT_SOURCE_DIR}/example/${mainfile} ${DIR_SRCS} ${DIR_SRCS01}
            ${DIR_SRCS02} ${DIR_SRCS03} ${DIR_SRCS04} ${DIR_SRCS05} ${DIR_SRCS06} ${DIR_SRCS07})

    add_dependencies(${mainname} ${LIBS})
    target_include_directories(${mainname} PUBLIC ${INCLUDES})
    target_link_libraries(${mainname} ${LIBS})
endforeach()

set(CMAKE_EXE_LINKER_FLAGS /NODEFAULTLIB:"MSVCRT.lib")
