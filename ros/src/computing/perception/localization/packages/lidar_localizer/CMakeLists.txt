cmake_minimum_required(VERSION 3.5)
project(lidar_localizer)

find_package(PCL REQUIRED)

IF (NOT (PCL_VERSION VERSION_LESS "1.7.2"))
    SET(PCL_OPENMP_PACKAGES pcl_omp_registration)
ENDIF (NOT (PCL_VERSION VERSION_LESS "1.7.2"))

find_package(OpenMP)
if (OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif ()

find_package(CUDA)

if (CUDA_FOUND)
    add_definitions(-DCUDA_FOUND)
    list(APPEND PCL_OPENMP_PACKAGES ndt_gpu)
endif ()

find_package(Eigen3 QUIET)

if (NOT EIGEN3_FOUND)
    # Fallback to cmake_modules
    find_package(cmake_modules REQUIRED)
    find_package(Eigen REQUIRED)
    set(EIGEN3_INCLUDE_DIRS ${EIGEN_INCLUDE_DIRS})
    set(EIGEN3_LIBRARIES ${EIGEN_LIBRARIES})  # Not strictly necessary as Eigen is head only
    # Possibly map additional variables to the EIGEN3_ prefix.
else ()
    set(EIGEN3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})
endif ()


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)

find_package(autoware_config_msgs REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(tf REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(velodyne_pointcloud REQUIRED)
find_package(ndt_tku REQUIRED)
find_package(ndt_cpu REQUIRED)
find_package(${PCL_OPENMP_PACKAGES} REQUIRED)

###########
## Build ##
###########

include_directories(include
 ${autoware_config_msgs_INCLUDE_DIRS}
 ${autoware_msgs_INCLUDE_DIRS} ${EIGEN3_INCLUDE_DIRS})

SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

add_executable(ndt_matching nodes/ndt_matching/ndt_matching.cpp)
target_link_libraries(ndt_matching)
ament_target_dependencies(ndt_matching
        autoware_msgs
        autoware_config_msgs
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ndt_tku
        ndt_cpu
        ${PCL_OPENMP_PACKAGES}
        )

add_executable(ndt_mapping nodes/ndt_mapping/ndt_mapping.cpp)
target_link_libraries(ndt_mapping)
ament_target_dependencies(ndt_mapping
        autoware_msgs
        autoware_config_msgs
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ndt_tku
        ndt_cpu
        ${PCL_OPENMP_PACKAGES}
        )

if (CUDA_FOUND)
    target_include_directories(ndt_matching PRIVATE ${CUDA_INCLUDE_DIRS})
    target_include_directories(ndt_mapping PRIVATE ${CUDA_INCLUDE_DIRS})
endif ()


if (NOT (PCL_VERSION VERSION_LESS "1.7.2"))
    set_target_properties(ndt_matching PROPERTIES COMPILE_DEFINITIONS "USE_PCL_OPENMP")
    set_target_properties(ndt_mapping PROPERTIES COMPILE_DEFINITIONS "USE_PCL_OPENMP")
endif (NOT (PCL_VERSION VERSION_LESS "1.7.2"))


add_executable(approximate_ndt_mapping nodes/approximate_ndt_mapping/approximate_ndt_mapping.cpp)
target_link_libraries(approximate_ndt_mapping)
ament_target_dependencies(approximate_ndt_mapping
        autoware_msgs
        autoware_config_msgs
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ndt_tku
        ndt_cpu
        ${PCL_OPENMP_PACKAGES}
        )

add_executable(queue_counter nodes/queue_counter/queue_counter.cpp)
target_link_libraries(queue_counter)
ament_target_dependencies(queue_counter
        autoware_msgs
        autoware_config_msgs
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ndt_tku
        ndt_cpu
        ${PCL_OPENMP_PACKAGES}
        )


add_executable(ndt_matching_tku nodes/ndt_matching_tku/ndt_matching_tku.cpp)
target_link_libraries(ndt_matching_tku)
ament_target_dependencies(ndt_matching_tku
        autoware_msgs
        autoware_config_msgs
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ndt_tku
        ndt_cpu
        ${PCL_OPENMP_PACKAGES}
        )

add_executable(ndt_mapping_tku nodes/ndt_mapping_tku/ndt_mapping_tku.cpp)
target_link_libraries(ndt_mapping_tku)
ament_target_dependencies(ndt_mapping_tku
        autoware_msgs
        autoware_config_msgs
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ndt_tku
        ndt_cpu
        ${PCL_OPENMP_PACKAGES}
        )

add_executable(mapping nodes/ndt_mapping_tku/mapping.cpp)
target_link_libraries(mapping)
ament_target_dependencies(mapping
        autoware_msgs
        autoware_config_msgs
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ndt_tku
        ndt_cpu
        ${PCL_OPENMP_PACKAGES}
        )

add_library(ndt_matching_monitor_lib SHARED
        nodes/ndt_matching_monitor/ndt_matching_monitor.h
        nodes/ndt_matching_monitor/ndt_matching_monitor.cpp
        )
target_include_directories(ndt_matching_monitor_lib PRIVATE
        ${autoware_config_msgs_INCLUDE_DIRS}
        ${autoware_msgs_INCLUDE_DIRS}
        ${PCL_INCLUDE_DIRS}
        )
ament_target_dependencies(ndt_matching_monitor_lib
        autoware_msgs
        autoware_config_msgs
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ndt_tku
        ndt_cpu
        ${PCL_OPENMP_PACKAGES}
        )
target_link_libraries(ndt_matching_monitor_lib)

add_executable(ndt_matching_monitor nodes/ndt_matching_monitor/ndt_matching_monitor_node.cpp)
target_link_libraries(ndt_matching_monitor ndt_matching_monitor_lib)

add_executable(icp_matching nodes/icp_matching/icp_matching.cpp)
target_link_libraries(icp_matching)
ament_target_dependencies(icp_matching
        autoware_msgs
        autoware_config_msgs
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ndt_tku
        ndt_cpu
        ${PCL_OPENMP_PACKAGES}
        )

install(TARGETS ndt_matching_tku ndt_mapping_tku ndt_mapping_tku ndt_matching_monitor
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME}
        )

install(TARGETS ndt_matching ndt_mapping approximate_ndt_mapping queue_counter
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME}
        )

install(TARGETS icp_matching
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME})


install(DIRECTORY launch/
        DESTINATION share/${PROJECT_NAME}/launch
        PATTERN ".svn" EXCLUDE)

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(autoware_config_msgs)
ament_export_dependencies(roscpp)
ament_export_dependencies(std_msgs)
ament_export_dependencies(nav_msgs)
ament_export_dependencies(tf)
ament_export_dependencies(pcl_ros)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(pcl_conversions)
ament_export_dependencies(velodyne_pointcloud)
ament_export_dependencies(ndt_tku)
ament_export_dependencies(ndt_cpu)
ament_export_dependencies(${PCL_OPENMP_PACKAGES})

ament_package()
