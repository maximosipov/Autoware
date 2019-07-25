cmake_minimum_required(VERSION 3.5)
project(op_planner)

# Get a ros version
set(ROS_VERSION $ENV{ROS_DISTRO})

find_package(ament_cmake REQUIRED)
find_package(autoware_build_flags REQUIRED)
find_package(cmake_modules REQUIRED)
find_package(op_utility REQUIRED)

find_package(OpenCV REQUIRED)
find_package(TinyXML REQUIRED)

###########
## Build ##
###########

include_directories(
		include
		${TinyXML_INCLUDE_DIRS}
		${op_utility_INCLUDE_DIRS}
)

set(PLANNERH_SRC
		src/BehaviorPrediction.cpp 
		src/BehaviorStateMachine.cpp
		src/DecisionMaker.cpp
		src/LocalPlannerH.cpp
		src/MappingHelpers.cpp
		src/MatrixOperations.cpp
		src/PassiveDecisionMaker.cpp
		src/PlannerH.cpp		
		src/PlanningHelpers.cpp				
		src/SimuDecisionMaker.cpp
		src/TrajectoryCosts.cpp
		src/TrajectoryDynamicCosts.cpp
)

## Declare a cpp library
add_library(${PROJECT_NAME}
	    ${PLANNERH_SRC}
)

target_link_libraries(${PROJECT_NAME}
		${OpenCV_LIBS}
		${TinyXML_LIBRARIES}
		${op_utility_LIBRARIES}
)

install(DIRECTORY include/${PROJECT_NAME}/
		DESTINATION include/${PROJECT_NAME}
		FILES_MATCHING PATTERN "*.h"
		)


install(TARGETS op_planner
		ARCHIVE DESTINATION lib
		LIBRARY DESTINATION lib
		RUNTIME DESTINATION lib/${PROJECT_NAME}
		)

ament_export_include_directories(include)

ament_export_libraries(${PROJECT_NAME})

ament_export_dependencies(op_utility)

ament_export_dependencies(OpenCV)
ament_export_dependencies(TinyXML)

ament_package()
