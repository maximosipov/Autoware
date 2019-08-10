cmake_minimum_required(VERSION 3.5)
project(obj_db)


find_package(autoware_build_flags REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(gnss REQUIRED)
find_package(visualization_msgs REQUIRED)
find_package(autoware_can_msgs REQUIRED)

set(CMAKE_CXX_FLAGS "-O2 -Wall ${CMAKE_CXX_FLAGS}")

###########
## Build ##
###########

include_directories(
        include
        ${roscpp_INCLUDE_DIRS}
        ${gnss_INCLUDE_DIRS}
        ${visualization_msgs_INCLUDE_DIRS}
        ${autoware_can_msgs_INCLUDE_DIRS}
)

add_library(obj_db
        lib/obj_db/SendData.cpp
        lib/obj_db/util.cpp
        )

add_executable(obj_downloader nodes/obj_downloader/obj_downloader.cpp)
target_link_libraries(obj_downloader
        obj_db
        ${roscpp_LIBRARIES}
        ${gnss_LIBRARIES}
)

add_executable(obj_uploader nodes/obj_uploader/obj_uploader.cpp)
target_link_libraries(obj_uploader
        obj_db
        ${roscpp_LIBRARIES}
)

set(CAMERA_YAML "$ENV{HOME}/.ros/autoware/camera_lidar_2d.yaml")

set_target_properties(obj_uploader
        PROPERTIES COMPILE_FLAGS
        "-DCAMERA_YAML=${CAMERA_YAML}")

add_executable(can_uploader nodes/can_uploader/can_uploader.cpp)
target_link_libraries(can_uploader
        obj_db
        ${roscpp_LIBRARIES}
)
ament_target_dependencies(can_uploader)

ament_export_dependencies(roscpp)
ament_export_dependencies(gnss)
ament_export_dependencies(visualization_msgs)
ament_export_dependencies(autoware_can_msgs)

ament_package()
