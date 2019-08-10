cmake_minimum_required(VERSION 3.5)
project(autoware_can_msgs)

find_package(ament_cmake REQUIRED)
find_package(message_generation REQUIRED)
find_package(std_msgs REQUIRED)


## Generate messages in the 'msg' folder
add_message_files(
  DIRECTORY msg
  FILES
    CANInfo.msg
    CANData.msg
    CANPacket.msg
)

## Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES
  std_msgs
)

ament_export_dependencies(std_msgs)

ament_package()
