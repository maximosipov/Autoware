cmake_minimum_required(VERSION 3.5)
project(autoware_config_msgs)

find_package(ament_cmake REQUIRED)
find_package(message_generation REQUIRED)
find_package(std_msgs REQUIRED)

## Generate messages in the 'msg' folder
add_message_files(
  DIRECTORY msg
  FILES

  ## Runtime Manager ##
  ConfigApproximateNDTMapping.msg
  ConfigCarDPM.msg
  ConfigCarFusion.msg
  ConfigCarKF.msg
  ConfigCompareMapFilter.msg
  ConfigDecisionMaker.msg
  ConfigDistanceFilter.msg
  ConfigICP.msg
  ConfigLaneRule.msg
  ConfigLaneSelect.msg
  ConfigLaneStop.msg
  ConfigLatticeVelocitySet.msg
  ConfigNDTMapping.msg
  ConfigNDTMappingOutput.msg
  ConfigNDT.msg
  ConfigPedestrianDPM.msg
  ConfigPedestrianFusion.msg
  ConfigPedestrianKF.msg
  ConfigPlannerSelector.msg
  ConfigPoints2Polygon.msg
  ConfigRandomFilter.msg
  ConfigRayGroundFilter.msg
  ConfigRcnn.msg
  ConfigRingFilter.msg
  ConfigRingGroundFilter.msg
  ConfigSSD.msg
  ConfigTwistFilter.msg
  ConfigVelocitySet.msg
  ConfigVoxelGridFilter.msg
  ConfigWaypointFollower.msg
  ConfigWaypointLoader.msg
)

## Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES
  std_msgs
)

ament_export_dependencies(std_msgs)

ament_package()
