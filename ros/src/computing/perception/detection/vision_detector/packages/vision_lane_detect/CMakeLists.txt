cmake_minimum_required(VERSION 3.5)
project(vision_lane_detect)


find_package(autoware_msgs REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(image_transport REQUIRED)
find_package(roscpp REQUIRED)
find_package(OpenCV REQUIRED)
find_package(OpenGL REQUIRED)

###########
## Build ##
###########

include_directories(
        ${autoware_msgs_INCLUDE_DIRS}
        nodes/vision_lane_detect
        ${OpenCV_INCLUDE_DIRS}
)

add_executable(vision_lane_detect
        nodes/vision_lane_detect/vision_lane_detect.cpp
        )

target_link_libraries(vision_lane_detect
        ${OpenCV_LIBRARIES}
        ${OPENGL_LIBRARIES}
        )
ament_target_dependencies(vision_lane_detect
        cv_bridge
        image_transport
        roscpp
        )

install(TARGETS vision_lane_detect
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME}
)

ament_export_dependencies(cv_bridge)
ament_export_dependencies(image_transport)
ament_export_dependencies(roscpp)

ament_package()
