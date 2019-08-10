cmake_minimum_required(VERSION 3.5)
project(map_file)

include(FindPkgConfig)

find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)

find_package(vector_map REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(visualization_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(tf REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(autoware_msgs REQUIRED)

find_package(PCL REQUIRED COMPONENTS io)
# See: https://github.com/ros-perception/perception_pcl/blob/lunar-devel/pcl_ros/CMakeLists.txt#L10-L22
if(NOT "${PCL_LIBRARIES}" STREQUAL "")
  # FIXME: this causes duplicates and not found error in ubuntu:zesty
  list(REMOVE_ITEM PCL_LIBRARIES "/usr/lib/libmpi.so")

  # For debian: https://github.com/ros-perception/perception_pcl/issues/139
  list(REMOVE_ITEM PCL_IO_LIBRARIES
    "vtkGUISupportQt"
    "vtkGUISupportQtOpenGL"
    "vtkGUISupportQtSQL"
    "vtkGUISupportQtWebkit"
    "vtkViewsQt"
    "vtkRenderingQt")
endif()

find_package(CURL REQUIRED)


set(CMAKE_CXX_FLAGS "-O2 -Wall ${CMAKE_CXX_FLAGS}")

###########
## Build ##
###########

include_directories(
  include
  ${vector_map_INCLUDE_DIRS}
  ${autoware_msgs_INCLUDE_DIRS}
  ${PCL_IO_INCLUDE_DIRS}
  ${CURL_INCLUDE_DIRS}
)

add_library(get_file
  lib/map_file/get_file.cpp
  )
target_link_libraries(get_file ${CURL_LIBRARIES})

add_executable(points_map_loader nodes/points_map_loader/points_map_loader.cpp)
target_link_libraries(points_map_loader
  get_file
  ${CURL_LIBRARIES}
  ${PCL_IO_LIBRARIES}
  ${roscpp_LIBRARIES}
  ${tf_LIBRARIES}
)
ament_target_dependencies(points_map_loader)

add_executable(vector_map_loader nodes/vector_map_loader/vector_map_loader.cpp)
target_link_libraries(vector_map_loader
  get_file
  ${vector_map_LIBRARIES}
  ${CURL_LIBRARIES}
  ${roscpp_LIBRARIES}
)

add_executable(points_map_filter nodes/points_map_filter/points_map_filter_node.cpp nodes/points_map_filter/points_map_filter.cpp)
target_link_libraries(points_map_filter
  ${roscpp_LIBRARIES}
  ${pcl_ros_LIBRARIES}
)
ament_target_dependencies(points_map_filter)

## Install executables and/or libraries
install(TARGETS get_file points_map_loader vector_map_loader
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME})

## Install project namespaced headers
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME})

ament_export_include_directories(include)

ament_export_libraries(get_file)

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(vector_map)
ament_export_dependencies(roscpp)
ament_export_dependencies(std_msgs)
ament_export_dependencies(visualization_msgs)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(tf)
ament_export_dependencies(pcl_ros)
ament_export_dependencies(tf2_ros)
ament_export_dependencies(tf2_geometry_msgs)
ament_export_dependencies(autoware_msgs)

ament_export_dependencies(PCL)
ament_export_dependencies(CURL)

ament_package()
