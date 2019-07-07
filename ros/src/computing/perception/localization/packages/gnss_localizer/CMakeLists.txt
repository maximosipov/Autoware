cmake_minimum_required(VERSION 3.5)
project(gnss_localizer)


find_package(autoware_build_flags REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(nmea_msgs REQUIRED)
find_package(gnss REQUIRED)
find_package(tf REQUIRED)

SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

include_directories(
        include
        ${roscpp_INCLUDE_DIRS}
        ${gnss_INCLUDE_DIRS}
        )

add_executable(fix2tfpose
        nodes/fix2tfpose/fix2tfpose.cpp
        )
target_link_libraries(fix2tfpose ${gnss_LIBRARIES})
ament_target_dependencies(fix2tfpose
        roscpp
        std_msgs
        nmea_msgs
        gnss
        tf
        )

add_executable(nmea2tfpose
        nodes/nmea2tfpose/nmea2tfpose_core.cpp
        nodes/nmea2tfpose/nmea2tfpose_node.cpp
        )
target_include_directories(nmea2tfpose PRIVATE nodes/nmea2tfpose)
target_link_libraries(nmea2tfpose ${gnss_LIBRARIES})
ament_target_dependencies(nmea2tfpose
        roscpp
        std_msgs
        nmea_msgs
        gnss
        tf
        )

install(TARGETS nmea2tfpose fix2tfpose
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY launch/
        DESTINATION share/${PROJECT_NAME}/launch
        PATTERN ".svn" EXCLUDE)

ament_export_dependencies(autoware_build_flags)
ament_export_dependencies(roscpp)
ament_export_dependencies(std_msgs)
ament_export_dependencies(nmea_msgs)
ament_export_dependencies(gnss)
ament_export_dependencies(tf)

ament_package()
