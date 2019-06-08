cmake_minimum_required(VERSION 3.5)
project(lidar_kf_contour_track)


find_package(autoware_build_flags REQUIRED)

find_package(vector_map_msgs REQUIRED)
find_package(autoware_msgs REQUIRED)
find_package(roscpp REQUIRED)
find_package(tf REQUIRED)
find_package(jsk_recognition_msgs REQUIRED)
find_package(op_ros_helpers REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(ament_cmake REQUIRED)

find_package(OpenCV REQUIRED)

include_directories(
        ${autoware_msgs_INCLUDE_DIRS}
        ${vector_map_msgs_INCLUDE_DIRS}
        ${OpenCV_INCLUDE_DIRS}
        include
)

#kl contour track
add_executable(lidar_kf_contour_track
  nodes/lidar_kf_contour_track/lidar_kf_contour_track.cpp
  nodes/lidar_kf_contour_track/lidar_kf_contour_track_core.cpp
  nodes/lidar_kf_contour_track/PolygonGenerator.cpp
  nodes/lidar_kf_contour_track/SimpleTracker.cpp
)
target_link_libraries(lidar_kf_contour_track
  ${OpenCV_LIBRARIES}
)
ament_target_dependencies(lidar_kf_contour_track
        autoware_msgs
        vector_map_msgs
        roscpp
        tf
        jsk_recognition_msgs
        op_ros_helpers
        cv_bridge
        )

install(TARGETS
        lidar_kf_contour_track
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
)

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(vector_map_msgs)
ament_export_dependencies(roscpp)
ament_export_dependencies(tf)
ament_export_dependencies(jsk_recognition_msgs)
ament_export_dependencies(op_ros_helpers)
ament_export_dependencies(cv_bridge)
ament_export_dependencies(ament_cmake)

if(BUILD_TESTING)
        find_package(ament_lint_auto REQUIRED)
        ament_lint_auto_find_test_dependencies()
endif()

ament_package()
