cmake_minimum_required(VERSION 3.5)
project(pcl_omp_registration)

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

IF (PCL_VERSION VERSION_LESS "1.7.2")
    message("pcl_omp requires PCL 1.7.2 or higher versions")
ELSE (PCL_VERSION VERSION_LESS "1.7.2")


    set(srcs
            src/ndt.cpp
            )

    set(incs
            include/pcl_omp_registration/ndt.h
            include/pcl_omp_registration/registration.h
            )

    set(impl_incs
            include/pcl_omp_registration/impl/ndt.hpp
            include/pcl_omp_registration/impl/registration.hpp
            )

    include_directories(include
            ${PCL_INCLUDE_DIRS}
            ${EIGEN3_INCLUDE_DIRS}
            )

    add_library(pcl_omp_registration
            ${srcs}
            ${incs}
            ${impl_incs}
            )

    target_link_libraries(pcl_omp_registration
            ${PCL_LIBRARIES}
            ${EIGEN3_LIBRARIES}
            )

    install(DIRECTORY include/${PROJECT_NAME}/
            DESTINATION include/${PROJECT_NAME}
            )

    install(TARGETS pcl_omp_registration
            ARCHIVE DESTINATION lib
            LIBRARY DESTINATION lib
            RUNTIME DESTINATION ib/${PROJECT_NAME}
            )


    ament_target_dependencies(${PROJECT_NAME}
            roscpp
            )

    ament_export_include_directories(include)

    ament_export_libraries(pcl_omp_registration)

    ament_export_dependencies(
            PCL
            Eigen3)

    ament_package()

ENDIF (PCL_VERSION VERSION_LESS "1.7.2")
