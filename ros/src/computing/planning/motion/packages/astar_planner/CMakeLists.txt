cmake_minimum_required(VERSION 3.5)
project(astar_planner)


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)

find_package(vector_map REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(tf REQUIRED)
find_package(waypoint_follower REQUIRED)

###########
## Build ##
###########

SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
  ${vector_map_INCLUDE_DIRS}
  ${autoware_msgs_INCLUDE_DIRS}
  ${waypoint_follower_INCLUDE_DIRS}
)

add_executable(velocity_set nodes/velocity_set/velocity_set.cpp nodes/velocity_set/velocity_set_path.cpp nodes/velocity_set/velocity_set_info.cpp nodes/velocity_set/libvelocity_set.cpp)
target_link_libraries(velocity_set
  ${roscpp_LIBRARIES}
  ${tf_LIBRARIES}
  ${pcl_ros_LIBRARIES}
  ${waypoint_follower_LIBRARIES}
)
ament_target_dependencies(velocity_set
)

add_executable(obstacle_avoid nodes/obstacle_avoid/obstacle_avoid.cpp nodes/obstacle_avoid/astar_search.cpp nodes/obstacle_avoid/search_info_ros.cpp nodes/obstacle_avoid/astar_util.cpp)
target_link_libraries(obstacle_avoid
  ${roscpp_LIBRARIES}
  ${tf_LIBRARIES}
  ${waypoint_follower_LIBRARIES}
)
ament_target_dependencies(obstacle_avoid
)

add_executable(obstacle_sim nodes/obstacle_avoid/obstacle_sim/obstacle_sim.cpp nodes/obstacle_avoid/obstacle_sim/obstacle_sim_node.cpp)
target_link_libraries(obstacle_sim
  ${roscpp_LIBRARIES}
  ${tf_LIBRARIES}
)
ament_target_dependencies(obstacle_sim
)

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(vector_map)
ament_export_dependencies(roscpp)
ament_export_dependencies(std_msgs)
ament_export_dependencies(pcl_ros)
ament_export_dependencies(pcl_conversions)
ament_export_dependencies(tf)
ament_export_dependencies(waypoint_follower)

ament_package()
