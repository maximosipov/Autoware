cmake_minimum_required(VERSION 3.5)
project(imm_ukf_pda_track)


find_package(autoware_build_flags REQUIRED)

find_package(autoware_msgs REQUIRED)
find_package(roscpp REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(tf REQUIRED)
find_package(vector_map REQUIRED)
find_package(ament_cmake REQUIRED)


set(CMAKE_CXX_FLAGS "-O2 -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
  ${autoware_msgs_INCLUDE_DIRS}
  ${pcl_ros_INCLUDE_DIRS}
  ${vector_map_INCLUDE_DIRS}
  include)

link_directories(${PCL_LIBRARY_DIRS})

#imm_ukf_pda
add_executable(imm_ukf_pda
        nodes/imm_ukf_pda/imm_ukf_pda_main.cpp
        nodes/imm_ukf_pda/imm_ukf_pda.cpp
        nodes/imm_ukf_pda/ukf.cpp
        )
target_link_libraries(imm_ukf_pda
        ${PCL_LIBRARIES}
        )
ament_target_dependencies(imm_ukf_pda
        autoware_msgs
        roscpp
        pcl_ros
        geometry_msgs
        tf
        vector_map
        )

install(TARGETS
        imm_ukf_pda
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
        )

ament_export_dependencies(autoware_msgs)
ament_export_dependencies(roscpp)
ament_export_dependencies(pcl_ros)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(tf)
ament_export_dependencies(ament_cmake)

if(BUILD_TESTING)
        find_package(ament_lint_auto REQUIRED)
        ament_lint_auto_find_test_dependencies()
endif()

ament_package()
