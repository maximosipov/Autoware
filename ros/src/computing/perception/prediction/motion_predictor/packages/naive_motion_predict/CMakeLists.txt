cmake_minimum_required(VERSION 3.5)
project(naive_motion_predict)

include(FindPkgConfig)


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(tf REQUIRED)
find_package(imm_ukf_pda_track REQUIRED)

set(CMAKE_CXX_FLAGS "-O2 -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
  include
  ${autoware_msgs_INCLUDE_DIRS}
)

#naive_motion_predict
add_executable(naive_motion_predict
        nodes/naive_motion_predict/naive_motion_predict_node.cpp
        nodes/naive_motion_predict/naive_motion_predict.cpp)

target_link_libraries(naive_motion_predict
        )

ament_target_dependencies(naive_motion_predict
        roscpp
        autoware_msgs
        sensor_msgs
        tf
        imm_ukf_pda_track
        )

install(TARGETS
        naive_motion_predict
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
        )

ament_export_dependencies(roscpp)
ament_export_dependencies(autoware_msgs)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(tf)
ament_export_dependencies(imm_ukf_pda_track)

ament_package()
