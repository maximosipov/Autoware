cmake_minimum_required(VERSION 3.5)
project(ndt_tku)

find_package(ament_cmake REQUIRED)

find_package(GLUT REQUIRED)

include_directories(
        include
        ${GLUT_INCLUDE_DIRS}
)

add_library(ndt_tku
        src/algebra.cpp
        src/newton.cpp
        src/manage_ND.cpp
        )

target_link_libraries(ndt_tku
        ${GLUT_LIBRARIES})

install(TARGETS ndt_tku
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(DIRECTORY include/
        DESTINATION include
        PATTERN ".svn" EXCLUDE
        )

ament_target_dependencies(${PROJECT_NAME}
        GLUT
        )

ament_export_include_directories(include)

ament_export_libraries(ndt_tku)

ament_export_dependencies(GLUT)

ament_package()
