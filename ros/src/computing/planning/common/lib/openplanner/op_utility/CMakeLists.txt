cmake_minimum_required(VERSION 3.5)
project(op_utility)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

set(ROS_VERSION $ENV{ROS_DISTRO})

find_package(autoware_build_flags REQUIRED)

find_package(ament_cmake REQUIRED)
find_package(vector_map_msgs REQUIRED)
find_package(vector_map_server REQUIRED)

find_package(TinyXML REQUIRED)

###########
## Build ##
###########

include_directories(
        include
        ${vector_map_msgs_INCLUDE_DIRS}
        )

set(UTILITYH_SRC
        src/DataRW.cpp
        src/UtilityH.cpp
        )

## Declare a cpp library
add_library(${PROJECT_NAME}
        ${UTILITYH_SRC}
        )

target_link_libraries(${PROJECT_NAME}
        ${OpenCV_LIBS}
        ${TinyXML_LIBRARIES}
        )

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION include/${PROJECT_NAME}
        FILES_MATCHING PATTERN "*.h"
        )


install(TARGETS op_utility
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME}
        )


ament_export_include_directories(include)

ament_export_libraries(${PROJECT_NAME})

ament_export_dependencies(vector_map_msgs)
ament_export_dependencies(vector_map_server)

ament_export_dependencies(TinyXML)

ament_package()
