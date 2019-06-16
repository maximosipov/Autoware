cmake_minimum_required(VERSION 3.5)
project(vision_dpm_ttic_detect)

include(FindPkgConfig)

find_package(autoware_msgs REQUIRED)

find_package(autoware_config_msgs REQUIRED)

find_package(ament_cmake REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(roscpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(libdpm_ttic REQUIRED)

find_package(CUDA)
find_package(OpenCV REQUIRED)

###########
## Build ##
###########

set(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
        ${autoware_config_msgs_INCLUDE_DIRS}
        ${autoware_msgs_INCLUDE_DIRS}
        ${libdpm_ttic_INCLUDE_DIRS}
)

if (CUDA_FOUND)
    include_directories(${CUDA_INCLUDE_DIRS})

    if ("${CMAKE_SYSTEM_PROCESSOR}" MATCHES "^arm")
        link_directories(/usr/lib/arm-linux-gnueabihf/tegra)
    endif ()
endif ()

## dpm_ttic ##
#this must be fixed to use cmake cuda commands
if (CUDA_FOUND)

    cuda_add_executable(vision_dpm_ttic_detect
            nodes/vision_dpm_ttic_detect/vision_dpm_ttic_detect.cpp
            )

    target_link_libraries(vision_dpm_ttic_detect
            ${OpenCV_LIBRARIES}
            ${CUDA_LIBRARIES}
            ${CUDA_CUBLAS_LIBRARIES}
            ${CUDA_curand_LIBRARY}
            ${libdpm_ttic_LIBRARY}
            )

    set_target_properties(vision_dpm_ttic_detect
            PROPERTIES
            COMPILE_FLAGS
            "-DHAS_GPU -DDEFAULT_CUBIN=${CATKIN_DEVEL_PREFIX}/lib/GPU_function.cubin -DMODEL_DIR=${CMAKE_CURRENT_SOURCE_DIR}/data/"
            )
else ()

    add_executable(vision_dpm_ttic_detect
            nodes/vision_dpm_ttic_detect/vision_dpm_ttic_detect.cpp
            )

    target_link_libraries(vision_dpm_ttic_detect
            ${OpenCV_LIBRARIES}
            ${libdpm_ttic_LIBRARY}
            )

    set_target_properties(vision_dpm_ttic_detect
            PROPERTIES
            COMPILE_FLAGS
            "-DMODEL_DIR=${CMAKE_CURRENT_SOURCE_DIR}/data/"
            )
endif ()

ament_target_dependencies(vision_dpm_ttic_detect
        cv_bridge
        roscpp
        sensor_msgs
        std_msgs
        libdpm_ttic
        )

install(TARGETS vision_dpm_ttic_detect
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME}
        )

ament_export_dependencies(cv_bridge)
ament_export_dependencies(roscpp)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(std_msgs)
ament_export_dependencies(libdpm_ttic)

ament_package()
