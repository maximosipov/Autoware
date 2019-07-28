cmake_minimum_required(VERSION 3.5)
project(lane_planner)


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)
find_package(autoware_config_msgs REQUIRED)

find_package(vector_map REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(tablet_socket REQUIRED)
find_package(waypoint_follower REQUIRED)
find_package(gnss REQUIRED)

set(CMAKE_CXX_FLAGS "-O2 -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
        include
        ${vector_map_INCLUDE_DIRS}
        ${autoware_msgs_INCLUDE_DIRS}
        ${tablet_socket_INCLUDE_DIRS}
        ${autoware_config_msgs_INCLUDE_DIRS}
        ${waypoint_follower_INCLUDE_DIRS}
)

add_library(lane_planner lib/lane_planner/lane_planner_vmap.cpp)
target_link_libraries(lane_planner
        ${roscpp_LIBRARIES}
        ${gnss_LIBRARIES}
        )
ament_target_dependencies(lane_planner
        )

add_executable(lane_navi nodes/lane_navi/lane_navi.cpp)
target_link_libraries(lane_navi
        lane_planner
        )
ament_target_dependencies(lane_navi
        )

add_executable(lane_rule nodes/lane_rule/lane_rule.cpp)
target_link_libraries(lane_rule
        lane_planner
        )
ament_target_dependencies(lane_rule
        )

add_executable(lane_stop nodes/lane_stop/lane_stop.cpp)
target_link_libraries(lane_stop
        lane_planner
        )
ament_target_dependencies(lane_stop
        )

add_executable(lane_select
        nodes/lane_select/lane_select_node.cpp
        nodes/lane_select/lane_select_core.cpp
        nodes/lane_select/hermite_curve.cpp
        )
target_link_libraries(lane_select
        ${roscpp_LIBRARIES}
        )
ament_target_dependencies(lane_select
        )

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION include/${PROJECT_NAME}
        FILES_MATCHING PATTERN "*.hpp"
        )

install(TARGETS
        lane_select
        lane_stop
        lane_rule
        lane_navi
        lane_planner
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
        )

ament_export_include_directories(include)

ament_export_libraries(lane_planner)

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(vector_map)
ament_export_dependencies(roscpp)
ament_export_dependencies(std_msgs)
ament_export_dependencies(tablet_socket)
ament_export_dependencies(waypoint_follower)
ament_export_dependencies(gnss)

ament_package()
