cmake_minimum_required(VERSION 3.5)
project(waypoint_follower)


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)

find_package(autoware_config_msgs REQUIRED)

find_package(tablet_socket_msgs REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(tf REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(autoware_msgs REQUIRED)
find_package(autoware_config_msgs REQUIRED)
find_package(tablet_socket_msgs REQUIRED)

################################################
## Declare ROS messages, services and actions ##
################################################

###########
## Build ##
###########

SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
        include
        ${autoware_config_msgs_INCLUDE_DIRS}
        ${autoware_msgs_INCLUDE_DIRS}
        ${tablet_socket_msgs_INCLUDE_DIRS}
)

add_library(libwaypoint_follower lib/libwaypoint_follower.cpp)
ament_target_dependencies(libwaypoint_follower
        )
target_link_libraries(libwaypoint_follower
        ${roscpp_LIBRARIES}
)

add_executable(pure_pursuit
        nodes/pure_pursuit/pure_pursuit_node.cpp
        nodes/pure_pursuit/pure_pursuit.cpp
        nodes/pure_pursuit/pure_pursuit_core.cpp
        nodes/pure_pursuit/pure_pursuit_viz.cpp
        )
target_link_libraries(pure_pursuit libwaypoint_follower)
ament_target_dependencies(pure_pursuit
)

add_executable(wf_simulator nodes/wf_simulator/wf_simulator.cpp)
target_link_libraries(wf_simulator
        libwaypoint_follower
        ${tf_LIBRARIES}
)

add_executable(twist_filter nodes/twist_filter/twist_filter.cpp)
target_link_libraries(twist_filter
        ${roscpp_LIBRARIES}
)
ament_target_dependencies(twist_filter
)

add_executable(twist_gate nodes/twist_gate/twist_gate.cpp)
target_link_libraries(twist_gate
        ${roscpp_LIBRARIES}
)
ament_target_dependencies(twist_gate
)

## Install executables and/or libraries
install(TARGETS libwaypoint_follower pure_pursuit wf_simulator twist_filter twist_gate
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME})

## Install project namespaced headers
install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION include/${PROJECT_NAME})

install(DIRECTORY launch/
        DESTINATION share/${PROJECT_NAME}/launch
        PATTERN ".svn" EXCLUDE)

ament_export_include_directories(include)

ament_export_libraries(libwaypoint_follower)
        
ament_export_dependencies(autoware_msgs)
ament_export_dependencies(autoware_config_msgs)
ament_export_dependencies(tablet_socket_msgs)
ament_export_dependencies(roscpp)
ament_export_dependencies(std_msgs)
ament_export_dependencies(tf)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(pcl_ros)
ament_export_dependencies(pcl_conversions)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(autoware_msgs)
ament_export_dependencies(autoware_config_msgs)
ament_export_dependencies(tablet_socket_msgs)

ament_package()
