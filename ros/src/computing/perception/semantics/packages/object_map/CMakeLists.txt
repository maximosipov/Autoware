cmake_minimum_required(VERSION 3.5)

project(object_map)


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)

find_package(vector_map REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(tf REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(vector_map REQUIRED)
find_package(grid_map_ros REQUIRED)
find_package(grid_map_cv REQUIRED)
find_package(grid_map_msgs REQUIRED)


find_package(Qt5Core REQUIRED)
find_package(OpenCV REQUIRED)
find_package(PCL REQUIRED)
find_package(OpenMP)

if (OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif ()

set(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
        ${vector_map_INCLUDE_DIRS}
        ${autoware_msgs_INCLUDE_DIRS}
        ${PCL_INCLUDE_DIRS}
        include
)
### object_map_utils ###
add_library(object_map_utils_lib
        include/object_map_utils.cpp
        include/object_map_utils.hpp
        )
target_link_libraries(object_map_utils_lib
        ${vector_map_LIBRARIES}
        ${grid_map_ros_LIBRARIES}
        ${OpenCV_LIBRARIES}
        )

### laserscan2costmap ###
add_executable(laserscan2costmap
        nodes/laserscan2costmap/laserscan2costmap.cpp
        )

target_link_libraries(laserscan2costmap
        ${roscpp_LIBRARIES}
        ${tf_LIBRARIES}
        )

### points2costmap ###
add_executable(points2costmap
        nodes/points2costmap/points2costmap.cpp
        )
target_link_libraries(points2costmap
        ${roscpp_LIBRARIES}
        ${PCL_LIBRARIES}
        )

### potential_field ###
add_executable(potential_field
        nodes/potential_field/potential_field.cpp
        )
ament_target_dependencies(potential_field
        )
target_link_libraries(potential_field
        ${roscpp_LIBRARIES}
        ${tf_LIBRARIES}
        ${grid_map_ros_LIBRARIES}
        ${PCL_LIBRARIES}
        )

### grid_map_filter ###
add_library(grid_map_filter_lib
        nodes/grid_map_filter/grid_map_filter.h
        include/object_map_utils.hpp
        nodes/grid_map_filter/grid_map_filter.cpp
        )
target_link_libraries(grid_map_filter_lib
        object_map_utils_lib
        )
add_executable(grid_map_filter
        nodes/grid_map_filter/grid_map_filter_node.cpp
        nodes/grid_map_filter/grid_map_filter.h
        )
target_link_libraries(grid_map_filter
        object_map_utils_lib
        grid_map_filter_lib
        )

### wayarea2grid ###
add_library(wayarea2grid_lib
        nodes/wayarea2grid/wayarea2grid.h
        include/object_map_utils.hpp
        nodes/wayarea2grid/wayarea2grid.cpp
        )
target_link_libraries(wayarea2grid_lib
        object_map_utils_lib
        )

add_executable(wayarea2grid
        nodes/wayarea2grid/wayarea2grid.h
        nodes/wayarea2grid/wayarea2grid_node.cpp
        )

target_link_libraries(wayarea2grid
        wayarea2grid_lib
        )

install(TARGETS wayarea2grid grid_map_filter potential_field points2costmap laserscan2costmap
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY launch/
        DESTINATION share/${PROJECT_NAME}/launch
        PATTERN ".svn" EXCLUDE)

install(DIRECTORY config/
        DESTINATION share/${PROJECT_NAME}/config
        PATTERN ".svn" EXCLUDE)

install(DIRECTORY include/
        DESTINATION include/${PROJECT_NAME}
        PATTERN ".svn" EXCLUDE
        )

ament_export_include_directories(include)

ament_export_libraries(wayarea2grid_lib)

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(vector_map)
ament_export_dependencies(roscpp)
ament_export_dependencies(pcl_ros)
ament_export_dependencies(pcl_conversions)
ament_export_dependencies(tf)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(nav_msgs)
ament_export_dependencies(vector_map)
ament_export_dependencies(grid_map_ros)
ament_export_dependencies(grid_map_cv)
ament_export_dependencies(grid_map_msgs)

ament_export_dependencies(Qt5Core)
ament_export_dependencies(OpenCV)
ament_export_dependencies(PCL)
ament_export_dependencies(OpenMP)

ament_package()
