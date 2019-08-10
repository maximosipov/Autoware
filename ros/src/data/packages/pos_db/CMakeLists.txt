cmake_minimum_required(VERSION 3.5)
project(pos_db)


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(gnss REQUIRED)
find_package(jsk_recognition_msgs REQUIRED)
find_package(tf REQUIRED)
find_package(jsk_rviz_plugins REQUIRED)

include(FindLibSsh2.cmake)

set(CMAKE_CXX_FLAGS "-O2 -Wall ${CMAKE_CXX_FLAGS}")

if (LIBSSH2_FOUND)

    include_directories(
            include
            ${autoware_msgs_INCLUDE_DIRS}
            ${LIBSSH2_INCLUDE_DIRS}
            ${tf_INCLUDE_DIRS}
            ${gnss_INCLUDE_DIRS}
    )

    add_library(pos_db
            lib/pos_db/SendData.cpp
            lib/pos_db/util.cpp
            )
    target_link_libraries(pos_db
            ${LIBSSH2_LIBRARIES}
            )

    add_executable(pos_downloader
            nodes/pos_downloader/pos_downloader.cpp)
    target_link_libraries(pos_downloader
            pos_db
            ${roscpp_LIBRARIES}
            ${gnss_LIBRARIES}
            ${LIBSSH2_LIBRARIES}
            )

    add_executable(pos_uploader
            nodes/pos_uploader/pos_uploader.cpp)
    target_link_libraries(pos_uploader
            pos_db
            ${roscpp_LIBRARIES}
            ${gnss_LIBRARIES}
            ${LIBSSH2_LIBRARIES}
            )
    ament_target_dependencies(pos_uploader
            )

    set(CAMERA_YAML "$ENV{HOME}/.ros/autoware/camera_lidar_2d.yaml")

    set_target_properties(pos_uploader
            PROPERTIES COMPILE_FLAGS
            "-DCAMERA_YAML=${CAMERA_YAML}")

    install(TARGETS
            pos_downloader
            pos_db
            pos_uploader
            ARCHIVE DESTINATION lib
            LIBRARY DESTINATION lib
            RUNTIME DESTINATION bin)

    install(DIRECTORY include
            DESTINATION include/${PROJECT_NAME})

    ament_export_include_directories(include)

    ament_export_libraries(pos_db)

    ament_export_dependencies(autoware_msgs)
    ament_export_dependencies(roscpp)
    ament_export_dependencies(gnss)
    ament_export_dependencies(jsk_recognition_msgs)
    ament_export_dependencies(tf)
    ament_export_dependencies(jsk_rviz_plugins)

    ament_package()
else()
    message("LibSSH2 not found. pos_db package won't be built.")
endif()
