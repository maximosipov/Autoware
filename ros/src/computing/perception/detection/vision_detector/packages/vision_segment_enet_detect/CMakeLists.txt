cmake_minimum_required(VERSION 3.5)
project(vision_segment_enet_detect)

FIND_PACKAGE(ament_cmake REQUIRED)
FIND_PACKAGE(cv_bridge REQUIRED)
FIND_PACKAGE(image_transport REQUIRED)
FIND_PACKAGE(roscpp REQUIRED)
FIND_PACKAGE(sensor_msgs REQUIRED)
FIND_PACKAGE(CUDA)
FIND_PACKAGE(OpenCV REQUIRED)

###########
## Build ##
###########

SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

INCLUDE_DIRECTORIES()


#####ENET########
##############################ENet's CAFFE FORK NEEDS TO BE PREVIOUSLY COMPILED####################
set(ENET_CAFFE_PATH "$ENV{HOME}/ENet/caffe-enet/distribute")
####################################################################################################
if (EXISTS "${ENET_CAFFE_PATH}" AND ${CUDA_FOUND})

  ADD_EXECUTABLE(vision_segment_enet_detect
    nodes/vision_segment_enet_detect/vision_segment_enet_detect_node.cpp
    nodes/vision_segment_enet_detect/vision_segment_enet_detect.cpp
    )

  TARGET_LINK_LIBRARIES(vision_segment_enet_detect
    ${OpenCV_LIBS}
    ${CUDA_LIBRARIES}
    ${CUDA_CUBLAS_LIBRARIES}
    ${CUDA_curand_LIBRARY}
    ${ENET_CAFFE_PATH}/lib/libcaffe.so
    glog
    )

  TARGET_INCLUDE_DIRECTORIES(vision_segment_enet_detect PRIVATE
    ${CUDA_INCLUDE_DIRS}
    ${ENET_CAFFE_PATH}/include
    include
    )

  ament_target_dependencies(vision_segment_enet_detect
    cv_bridge
    image_transport
    roscpp
    sensor_msgs
    )

  install(TARGETS
        vision_segment_enet_detect
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
  )

  ament_export_dependencies(cv_bridge)
  ament_export_dependencies(image_transport)
  ament_export_dependencies(roscpp)
  ament_export_dependencies(sensor_msgs)

  ament_package()
else ()
  message("' ENet/Caffe' is not installed. 'vision_segment_enet_detect' will not be built.")
endif ()
