cmake_minimum_required(VERSION 3.5)
project(vector_map)


find_package(autoware_build_flags REQUIRED)

find_package(vector_map_msgs REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(tf REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(visualization_msgs REQUIRED)

set(CMAKE_CXX_FLAGS "-O2 -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
  include
  ${vector_map_msgs_INCLUDE_DIRS}
  ${vector_map_INCLUDE_DIRS}
)

add_library(vector_map
  lib/vector_map/vector_map.cpp
)
ament_target_dependencies(vector_map
  ${vector_map_msgs_EXPORTED_TARGETS}
)
target_link_libraries(vector_map
  ${roscpp_LIBRARIES}
  ${vector_map_msgs_LIBRARIES}
)

## Install executables and/or libraries
install(TARGETS vector_map
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME})

## Install project namespaced headers
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME})

ament_export_include_directories(include)

ament_export_libraries(vector_map)

ament_export_dependencies(vector_map_msgs)
ament_export_dependencies(roscpp)
ament_export_dependencies(tf)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(visualization_msgs)

ament_package()
