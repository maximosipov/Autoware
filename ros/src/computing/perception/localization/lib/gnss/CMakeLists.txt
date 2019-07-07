cmake_minimum_required(VERSION 3.5)
project(gnss)

find_package(ament_cmake REQUIRED)
find_package(roscpp REQUIRED)

include_directories(
        include
)

add_library(gnss
        src/geo_pos_conv.cpp
        )

target_link_libraries(gnss
        )

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION include/${PROJECT_NAME}
        FILES_MATCHING PATTERN "*.hpp"
        )

ament_target_dependencies(${PROJECT_NAME}
        roscpp
        )

install(TARGETS gnss
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME}
        )

ament_export_include_directories(include)

ament_export_libraries(gnss)

ament_export_dependencies(roscpp)

ament_package()
