cmake_minimum_required(VERSION 3.5)
project(vision_ssd_detect)

set(ROS_VERSION $ENV{ROS_DISTRO})

include(FindPkgConfig)

find_package(autoware_build_flags REQUIRED)
find_package(autoware_msgs REQUIRED)
find_package(autoware_config_msgs REQUIRED)

find_package(ament_cmake REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(image_transport REQUIRED)
find_package(roscpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
FIND_PACKAGE(tf REQUIRED)

FIND_PACKAGE(CUDA)
FIND_PACKAGE(OpenCV REQUIRED)

###########
## Build ##
###########

set(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

include_directories()

IF (CUDA_FOUND)
    include_directories(${CUDA_INCLUDE_DIRS})

    IF ("${CMAKE_SYSTEM_PROCESSOR}" MATCHES "^arm")
        link_directories(/usr/lib/arm-linux-gnueabihf/tegra)
    endif ()
ENDIF ()


#####SSD########
##############################SSD'sFORK of CAFFE NEEDS TO BE PREVIOUSLY COMPILED####################
set(SSD_CAFFE_PATH "$ENV{HOME}/ssdcaffe/distribute")
####################################################################################################
if (EXISTS "${SSD_CAFFE_PATH}")

    add_executable(vision_ssd_detect
            src/vision_ssd_detect.cpp
            src/vision_ssd_detect_node.cpp
            )

    target_link_libraries(vision_ssd_detect
            ${OpenCV_LIBRARIES}
            ${CUDA_LIBRARIES}
            ${CUDA_CUBLAS_LIBRARIES}
            ${CUDA_curand_LIBRARY}
            ${SSD_CAFFE_PATH}/lib/libcaffe.so
            glog
            )

    target_include_directories(vision_ssd_detect PRIVATE
            ${autoware_msgs_INCLUDE_DIRS}
            ${autoware_config_msgs_INCLUDE_DIRS}
            ${SSD_CAFFE_PATH}/include
            include
            )

    ament_target_dependencies(vision_ssd_detect
            cv_bridge
            image_transport
            roscpp
            sensor_msgs
            std_msgs
            tf
            )

    install(TARGETS vision_ssd_detect
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
else ()
    message("'SSD/Caffe' is not installed. 'ssd_unc' will not be built.")
endif ()
