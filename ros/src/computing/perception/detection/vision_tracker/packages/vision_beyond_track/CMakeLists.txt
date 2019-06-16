cmake_minimum_required(VERSION 3.5)
project(vision_beyond_track)

find_package(OpenCV REQUIRED)

find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(image_transport REQUIRED)
find_package(roscpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(tf REQUIRED)

include_directories(
        include
        lib
        ${OpenCV_INCLUDE_DIRS}
        ${autoware_msgs_INCLUDE_DIRS}
)

add_library(
        beyond_track_lib STATIC
        lib/hungarian.cpp
        lib/clipper.cpp
)

add_executable(vision_beyond_track
        src/vision_beyond_track_node.cpp
        src/vision_beyond_track.cpp
)

target_link_libraries(vision_beyond_track
        ${OpenCV_LIBS}
        beyond_track_lib
)

ament_target_dependencies(vision_beyond_track
        cv_bridge
        image_transport
        roscpp
        sensor_msgs
        std_msgs
        tf
)

install(TARGETS beyond_track_lib vision_beyond_track
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME}
        )

ament_export_dependencies(cv_bridge)
ament_export_dependencies(image_transport)
ament_export_dependencies(roscpp)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(std_msgs)
ament_export_dependencies(tf)

ament_package()
