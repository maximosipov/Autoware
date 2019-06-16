cmake_minimum_required(VERSION 3.5)
project(lidar_shape_estimation)

find_package(ament_cmake REQUIRED)
find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(roscpp REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)

find_package(PCL 1.7 REQUIRED)
find_package(OpenCV REQUIRED)

include_directories(
        include
        ${PCL_INCLUDE_DIR}
        ${OpenCV_INCLUDE_DIRS}
        )

add_executable(lidar_shape_estimation
        src/main.cpp
        src/node.cpp
        src/shape_estimator.cpp
        src/model/bounding_box.cpp
        src/model/convex_hull.cpp
        src/model/cylinder.cpp
        )

target_link_libraries(lidar_shape_estimation
        ${OpenCV_LIBS}
        ${PCL_LIBRARIES}
        )

ament_target_dependencies(lidar_shape_estimation
        autoware_msgs
        pcl_ros
        roscpp
        tf2
        tf2_geometry_msgs
        )

install(TARGETS lidar_shape_estimation
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
        )

install(DIRECTORY launch/
        DESTINATION include
        FILES_MATCHING PATTERN "*.launch"
        )

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(pcl_ros)
ament_export_dependencies(roscpp)
ament_export_dependencies(tf2)
ament_export_dependencies(tf2_geometry_msgs)

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_shape_estimation.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()
if (BUILD_TESTING)
    find_package(rostest REQUIRED)
    find_package(rosunit REQUIRED)
    add_rostest_gtest(test-lidar_shape_estimation 
        test/test_lidar_shape_estimation.test
        test/src/test_lidar_shape_estimation.cpp
        src/shape_estimator.cpp
        src/model/bounding_box.cpp
        src/model/convex_hull.cpp
        src/model/cylinder.cpp)
    target_link_libraries(test-lidar_shape_estimation 
        ${OpenCV_LIBS}
        ${PCL_LIBRARIES})
endif ()
## Add folders to be run by python nosetests
# catkin_add_nosetests(test)

ament_package()
