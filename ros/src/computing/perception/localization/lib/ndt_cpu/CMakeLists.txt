cmake_minimum_required(VERSION 3.5)
project(ndt_cpu)

find_package(ament_cmake REQUIRED)
find_package(PCL REQUIRED)

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

include_directories(
        ${PCL_INCLUDE_DIRS}
        "include"
        ${EIGEN3_INCLUDE_DIRS}
)

set(srcs
        src/NormalDistributionsTransform.cpp
        src/Registration.cpp
        src/VoxelGrid.cpp
        src/Octree.cpp
        )

set(incs
        include/ndt_cpu/debug.h
        include/ndt_cpu/NormalDistributionsTransform.h
        include/ndt_cpu/Registration.h
        include/ndt_cpu/SymmetricEigenSolver.h
        include/ndt_cpu/VoxelGrid.h
        include/ndt_cpu/Octree.h
        )

add_library(ndt_cpu ${incs} ${srcs})

target_link_libraries(ndt_cpu
        ${PCL_LIBRARIES}
        )

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION include/${PROJECT_NAME}
        FILES_MATCHING PATTERN "*.h"
        )

install(TARGETS ndt_cpu
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION lib/${PROJECT_NAME}
        )

ament_target_dependencies(${PROJECT_NAME}
        PCL
        Eigen3
)


ament_export_include_directories(include)

ament_export_libraries(ndt_cpu)

ament_export_dependencies(PCL)
ament_export_dependencies(Eigen3)

ament_package()
