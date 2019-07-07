cmake_minimum_required(VERSION 3.5)
project(autoware_connector)


find_package(autoware_msgs REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(autoware_build_flags REQUIRED)
find_package(roscpp REQUIRED)
find_package(tf REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(autoware_can_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(std_msgs REQUIRED)

include_directories(
        ${autoware_msgs_INCLUDE_DIRS}
)

add_executable(can_status_translator
        nodes/can_status_translator/can_status_translator_node.cpp
        nodes/can_status_translator/can_status_translator_core.cpp
        )

target_link_libraries(can_status_translator)

add_executable(can_odometry
        nodes/can_odometry/can_odometry_node.cpp
        nodes/can_odometry/can_odometry_core.cpp
        )

target_link_libraries(can_odometry)

install(TARGETS can_status_translator
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME})

install(TARGETS can_odometry
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY launch/
        DESTINATION share/${PROJECT_NAME}/launch
        PATTERN ".svn" EXCLUDE)

ament_target_dependencies(can_status_translator
        autoware_msgs
        autoware_build_flags
        roscpp
        tf
        geometry_msgs
        autoware_can_msgs
        nav_msgs
        std_msgs
        )

ament_target_dependencies(can_odometry
        autoware_msgs
        autoware_build_flags
        roscpp
        tf
        geometry_msgs
        autoware_can_msgs
        nav_msgs
        std_msgs
        )

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(autoware_build_flags)
ament_export_dependencies(roscpp)
ament_export_dependencies(tf)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(autoware_can_msgs)
ament_export_dependencies(nav_msgs)
ament_export_dependencies(std_msgs)

ament_package()
