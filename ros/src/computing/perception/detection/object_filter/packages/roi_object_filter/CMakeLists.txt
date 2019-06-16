cmake_minimum_required(VERSION 2.8.3)
project(roi_object_filter)

find_package(ament_cmake REQUIRED)
find_package(autoware_build_flags REQUIRED)

find_package(roscpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(autoware_msgs REQUIRED)
find_package(grid_map_ros REQUIRED)
find_package(grid_map_cv REQUIRED)
find_package(grid_map_msgs REQUIRED)
find_package(tf REQUIRED)

find_package(OpenCV REQUIRED)
find_package(OpenMP QUIET)
find_package(Eigen3 QUIET)

if (NOT EIGEN3_FOUND)
    # Fallback to cmake_modules
    find_package(cmake_modules REQUIRED)
    find_package(Eigen REQUIRED)
    set(EIGEN3_INCLUDE_DIRS ${EIGEN_INCLUDE_DIRS})
    set(EIGEN3_LIBRARIES ${EIGEN_LIBRARIES})  # Not strictly necessary as Eigen is head only
    # Possibly map additional variables to the EIGEN3_ prefix.
else ()
    set(EIGEN3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})
endif ()

find_package(PkgConfig REQUIRED)
pkg_check_modules(YAML_CPP REQUIRED yaml-cpp)
find_path(YAML_CPP_INCLUDE_DIR NAMES yaml_cpp.h PATHS ${YAML_CPP_INCLUDE_DIRS})
find_library(YAML_CPP_LIBRARY NAMES YAML_CPP PATHS ${YAML_CPP_LIBRARIES})

include_directories(
        include
)

#lib
add_library(roi_object_filter_lib SHARED
        src/roi_object_filter.cpp
        include/roi_object_filter/roi_object_filter.h
        )

if (OPENMP_FOUND)
    set_target_properties(roi_object_filter_lib PROPERTIES
            COMPILE_FLAGS ${OpenMP_CXX_FLAGS}
            LINK_FLAGS ${OpenMP_CXX_FLAGS}
            )
endif ()

target_include_directories(roi_object_filter_lib PRIVATE
        ${OpenCV_INCLUDE_DIR}
        ${EIGEN3_INCLUDE_DIRS}
        ${YAML_CPP_INCLUDE_DIRS}
        include
        )

target_link_libraries(roi_object_filter_lib
        ${OpenCV_LIBRARIES}
        ${EIGEN_LIBRARIES}
        ${YAML_CPP_LIBRARIES}
        )

ament_target_dependencies(roi_object_filter_lib
        roscpp
        sensor_msgs
        geometry_msgs
        autoware_msgs
        grid_map_ros
        grid_map_cv
        grid_map_msgs
        tf
        )

#node
add_executable(roi_object_filter
        src/roi_object_filter_node.cpp
        )
target_include_directories(roi_object_filter PRIVATE
        ${OpenCV_INCLUDE_DIR}
        include
        )

target_link_libraries(roi_object_filter
        roi_object_filter_lib)

install(TARGETS roi_object_filter
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

ament_export_dependencies(roscpp)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(autoware_msgs)
ament_export_dependencies(grid_map_ros)
ament_export_dependencies(grid_map_cv)
ament_export_dependencies(grid_map_msgs)
ament_export_dependencies(tf)

if (CATKIN_ENABLE_TESTING)
    find_package(rostest REQUIRED)
    find_package(rosunit REQUIRED)
    add_rostest_gtest(test-roi_object_filter test/test_roi_object_filter.test test/src/test_roi_object_filter.cpp)
    target_link_libraries(test-roi_object_filter roi_object_filter_lib)
endif ()

ament_package()
