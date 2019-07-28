cmake_minimum_required(VERSION 3.5)
project(freespace_planner)


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(visualization_msgs REQUIRED)
find_package(tf REQUIRED)

set(CMAKE_CXX_FLAGS "-O2 -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
  include
  ${autoware_msgs_INCLUDE_DIRS}
)

add_executable(astar_navi
  nodes/astar_navi/astar_navi.cpp
  nodes/astar_navi/astar_search.cpp
  nodes/astar_navi/search_info_ros.cpp
  include/astar_util.cpp)

target_link_libraries(astar_navi
)

ament_target_dependencies(astar_navi
  autoware_msgs
  roscpp
  nav_msgs
  visualization_msgs
  tf
)

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(roscpp)
ament_export_dependencies(nav_msgs)
ament_export_dependencies(visualization_msgs)
ament_export_dependencies(tf)

ament_package()
