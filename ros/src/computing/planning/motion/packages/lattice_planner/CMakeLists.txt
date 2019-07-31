cmake_minimum_required(VERSION 3.5)
project(lattice_planner)


find_package(autoware_build_flags REQUIRED)

find_package(vector_map REQUIRED)
find_package(ament_cmake REQUIRED )
find_package(roscpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(tf REQUIRED)
find_package(gnss REQUIRED)
find_package(waypoint_follower REQUIRED)
find_package(astar_planner REQUIRED)
find_package(autoware_can_msgs REQUIRED)
find_package(autoware_msgs REQUIRED)
find_package(autoware_config_msgs REQUIRED)

find_package(Armadillo REQUIRED)

OPTION(USE_OpenMP "Use OpenMP" ON)
IF (USE_OpenMP)
    FIND_PACKAGE(OpenMP)
    IF (OPENMP_FOUND)
        SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
        SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    ENDIF ()
ENDIF ()


SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
        include
        ${ARMADILLO_INCLUDE_DIRS}
        ${vector_map_INCLUDE_DIRS}
        ${pcl_conversions_INCLUDE_DIRS}
        ${autoware_msgs_INCLUDE_DIRS}
        ${autoware_can_msgs_INCLUDE_DIRS}
        ${autoware_config_msgs_INCLUDE_DIRS}
        ${waypoint_follower_INCLUDE_DIRS}
)

add_library(libtraj_gen lib/libtraj_gen.cpp)
target_link_libraries(libtraj_gen ${ARMADILLO_LIBRARIES})

add_executable(lattice_trajectory_gen nodes/lattice_trajectory_gen/lattice_trajectory_gen.cpp)
target_link_libraries(lattice_trajectory_gen libtraj_gen
        ${pcl_ros_LIBRARIES}
        ${waypoint_follower_LIBRARIES}
)
ament_target_dependencies(lattice_trajectory_gen
)

add_executable(lattice_twist_convert nodes/lattice_twist_convert/lattice_twist_convert.cpp)
target_link_libraries(lattice_twist_convert libtraj_gen
        ${roscpp_LIBRARIES}
)
ament_target_dependencies(lattice_twist_convert
)

add_executable(lattice_velocity_set nodes/lattice_velocity_set/lattice_velocity_set.cpp nodes/lattice_velocity_set/libvelocity_set.cpp)
target_link_libraries(lattice_velocity_set
        ${roscpp_LIBRARIES}
        ${pcl_ros_LIBRARIES}
        ${waypoint_follower_LIBRARIES}
)
ament_target_dependencies(lattice_velocity_set
)

add_executable(path_select nodes/path_select/path_select.cpp)
target_link_libraries(path_select
        ${roscpp_LIBRARIES}
)
ament_target_dependencies(path_select
)

ament_export_dependencies(vector_map)
ament_export_dependencies(roscpp)
ament_export_dependencies(std_msgs)
ament_export_dependencies(pcl_ros)
ament_export_dependencies(pcl_conversions)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(tf)
ament_export_dependencies(gnss)
ament_export_dependencies(waypoint_follower)
ament_export_dependencies(astar_planner)
ament_export_dependencies(autoware_can_msgs)

ament_export_dependencies(Armadillo)

ament_package()
