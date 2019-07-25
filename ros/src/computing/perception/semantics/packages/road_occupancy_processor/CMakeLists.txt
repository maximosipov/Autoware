cmake_minimum_required(VERSION 3.5)
project(road_occupancy_processor)


find_package(autoware_build_flags REQUIRED)
find_package(ament_cmake REQUIRED)

find_package(vector_map REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(image_transport REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(roscpp REQUIRED)
find_package(tf REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(grid_map_ros REQUIRED)
find_package(grid_map_cv REQUIRED)
find_package(grid_map_msgs REQUIRED)

find_package(Qt5Core REQUIRED)
find_package(OpenCV REQUIRED)
find_package(PCL REQUIRED)
find_package(OpenMP)

set(CMAKE_CXX_FLAGS "-O3 -g -Wall ${CMAKE_CXX_FLAGS}")

#Road Occupancy Processor Library
add_library(road_occupancy_processor_lib SHARED
    include/road_occupancy_processor.h
    src/road_occupancy_processor.cpp
)

if (OPENMP_FOUND)
    set_target_properties(road_occupancy_processor_lib PROPERTIES
        COMPILE_FLAGS ${OpenMP_CXX_FLAGS}
        LINK_FLAGS ${OpenMP_CXX_FLAGS}
    )
endif()

target_include_directories(road_occupancy_processor_lib PRIVATE
    ${OpenCV_INCLUDE_DIR}
    ${vector_map_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
    include
)

target_link_libraries(road_occupancy_processor_lib
    ${OpenCV_LIBRARIES}
    ${PCL_LIBRARIES}
    ${Qt5Core_LIBRARIES}
    ${roscpp_LIBRARIES}
    ${grid_map_ros_LIBRARIES}
    ${tf_LIBRARIES}
    ${pcl_ros_LIBRARIES}
)

ament_target_dependencies(road_occupancy_processor_lib
)

#Occupancy Processor Main
add_executable(road_occupancy_processor
    src/road_occupancy_processor_node.cpp
)
target_include_directories(road_occupancy_processor PRIVATE
    ${OpenCV_INCLUDE_DIR}
    ${vector_map_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
    include)

target_link_libraries(road_occupancy_processor
    road_occupancy_processor_lib)

install(TARGETS road_occupancy_processor
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION ib/${PROJECT_NAME})

install(DIRECTORY launch/
        DESTINATION share/${PROJECT_NAME}/launch
        PATTERN ".svn" EXCLUDE)

ament_export_libraries(road_occupancy_processor_lib)

ament_export_dependencies(vector_map)
ament_export_dependencies(cv_bridge)
ament_export_dependencies(image_transport)
ament_export_dependencies(pcl_conversions)
ament_export_dependencies(pcl_ros)
ament_export_dependencies(roscpp)
ament_export_dependencies(tf)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(nav_msgs)
ament_export_dependencies(grid_map_ros)
ament_export_dependencies(grid_map_cv)
ament_export_dependencies(grid_map_msgs)

ament_export_dependencies(Qt5Core)
ament_export_dependencies(OpenCV)
ament_export_dependencies(PCL)
ament_export_dependencies(OpenMP)

ament_package()
