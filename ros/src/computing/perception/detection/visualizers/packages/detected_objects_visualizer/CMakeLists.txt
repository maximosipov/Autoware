cmake_minimum_required(VERSION 3.5)
project(detected_objects_visualizer)

find_package(ament_cmake REQUIRED)
find_package(autoware_build_flags REQUIRED)
find_package(roscpp REQUIRED)
find_package(autoware_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(tf REQUIRED)
find_package(visualization_msgs REQUIRED)
find_package(image_transport REQUIRED)
find_package(cv_bridge REQUIRED)

find_package(OpenCV REQUIRED)

find_package(Eigen3 QUIET)

if (NOT EIGEN3_FOUND)
    find_package(cmake_modules REQUIRED)
    find_package(Eigen REQUIRED)
    set(EIGEN3_INCLUDE_DIRS ${EIGEN_INCLUDE_DIRS})
    set(EIGEN3_LIBRARIES ${EIGEN_LIBRARIES})
else ()
    set(EIGEN3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})
endif ()

#Rviz Marker visualization
add_executable(visualize_detected_objects
        include/visualize_detected_objects.h
        src/visualize_detected_objects_main.cpp
        src/visualize_detected_objects.cpp
        )

target_include_directories(visualize_detected_objects PRIVATE
        ${OpenCV_INCLUDE_DIR}
        ${Boost_INCLUDE_DIRS}
        ${EIGEN3_INCLUDE_DIRS}
        ${cv_bridge_INCLUDE_DIRS}
        include
        )

target_link_libraries(visualize_detected_objects
        ${OpenCV_LIBRARIES}
        ${EIGEN3_LIBRARIES}
        ${cv_bridge_LIBRARIES}
        )

ament_target_dependencies(visualize_detected_objects
        roscpp
        autoware_msgs
        geometry_msgs
        sensor_msgs
        tf
        visualization_msgs
        image_transport
        cv_bridge
        )

#Image Rect Visualization
add_executable(visualize_rects
        include/visualize_rects.h
        src/visualize_rects_main.cpp
        src/visualize_rects.cpp
        )

target_include_directories(visualize_rects PRIVATE
        ${OpenCV_INCLUDE_DIR}
        ${cv_bridge_INCLUDE_DIRS}
        include
        )

target_link_libraries(visualize_rects
        ${OpenCV_LIBRARIES}
        ${cv_bridge_LIBRARIES}
        )

ament_target_dependencies(visualize_rects
        roscpp
        autoware_msgs
        geometry_msgs
        sensor_msgs
        tf
        visualization_msgs
        image_transport
        cv_bridge
        )

install(TARGETS
        visualize_detected_objects visualize_rects
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
        )

ament_export_dependencies(roscpp)
ament_export_dependencies(autoware_msgs)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(tf)
ament_export_dependencies(visualization_msgs)
ament_export_dependencies(image_transport)
ament_export_dependencies(cv_bridge)

ament_package()
