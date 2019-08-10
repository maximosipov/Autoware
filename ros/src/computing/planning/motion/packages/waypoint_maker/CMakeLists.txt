cmake_minimum_required(VERSION 3.5)
project(waypoint_maker)


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)

find_package(vector_map REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(gnss REQUIRED)
find_package(roscpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(tf REQUIRED)
find_package(waypoint_follower REQUIRED)
find_package(lane_planner REQUIRED)
find_package(autoware_msgs REQUIRED)
find_package(vector_map REQUIRED)
find_package(message_filters REQUIRED)

find_package(Boost REQUIRED)

SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
        ${lane_planner_INCLUDE_DIRS}
        ${waypoint_follower_INCLUDE_DIRS}
        ${vector_map_INCLUDE_DIRS}
        ${autoware_msgs_INCLUDE_DIRS}
        ${Boost_INCLUDE_DIRS}
)

add_executable(waypoint_loader nodes/waypoint_loader/waypoint_loader_core.cpp nodes/waypoint_loader/velocity_replanner.cpp nodes/waypoint_loader/waypoint_loader_node.cpp)
target_link_libraries(waypoint_loader
        ${roscpp_LIBRARIES}
)
ament_target_dependencies(waypoint_loader
)

add_executable(waypoint_saver nodes/waypoint_saver/waypoint_saver.cpp)
target_link_libraries(waypoint_saver
        ${roscpp_LIBRARIES}
        ${message_filters_LIBRARIES}
)
ament_target_dependencies(waypoint_saver
)

add_executable(waypoint_clicker nodes/waypoint_clicker/waypoint_clicker.cpp)
target_link_libraries(waypoint_clicker
        ${roscpp_LIBRARIES}
        ${lane_planner_LIBRARIES}
        ${tf_LIBRARIES}
)

add_executable(waypoint_marker_publisher nodes/waypoint_marker_publisher/waypoint_marker_publisher.cpp)
target_link_libraries(waypoint_marker_publisher
        ${roscpp_LIBRARIES}
        ${message_filters_LIBRARIES}
        ${waypoint_follower_LIBRARIES}
)
ament_target_dependencies(waypoint_marker_publisher
)

add_executable(waypoint_velocity_visualizer nodes/waypoint_velocity_visualizer/waypoint_velocity_visualizer.cpp)
target_link_libraries(waypoint_velocity_visualizer
        ${roscpp_LIBRARIES}
        ${message_filters_LIBRARIES}
        ${Boost_LIBRARIES}
)
ament_target_dependencies(waypoint_velocity_visualizer
)

install(TARGETS
        waypoint_loader
        waypoint_saver
        waypoint_clicker
        waypoint_marker_publisher
        waypoint_velocity_visualizer
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
        )


ament_export_dependencies(autoware_msgs)
ament_export_dependencies(vector_map)
ament_export_dependencies(gnss)
ament_export_dependencies(roscpp)
ament_export_dependencies(std_msgs)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(nav_msgs)
ament_export_dependencies(tf)
ament_export_dependencies(waypoint_follower)
ament_export_dependencies(lane_planner)
ament_export_dependencies(autoware_msgs)
ament_export_dependencies(vector_map)
ament_export_dependencies(message_filters)
ament_export_dependencies(Boost)

ament_package()
