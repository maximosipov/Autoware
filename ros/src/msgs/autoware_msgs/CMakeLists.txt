cmake_minimum_required(VERSION 3.5)
project(autoware_msgs)

find_package(ament_cmake REQUIRED)
find_package(message_generation REQUIRED)
find_package(std_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(jsk_recognition_msgs REQUIRED)


## Generate messages in the 'msg' folder
add_message_files(
        DIRECTORY msg
        FILES
            ControlCommandStamped.msg
            CloudCluster.msg
            CloudClusterArray.msg
            ColorSet.msg
            ControlCommand.msg
            DetectedObject.msg
            DetectedObjectArray.msg
            ExtractedPosition.msg
            ImageLaneObjects.msg
            ImageObjects.msg
            LaneArray.msg
            PointsImage.msg
            ScanImage.msg
            Signals.msg
            TunedResult.msg
            ValueSet.msg
            Centroids.msg
            DTLane.msg
            GeometricRectangle.msg
            ICPStat.msg
            ImageObj.msg
            ImageObjRanged.msg
            ImageObjTracked.msg
            ImageRect.msg
            ImageRectRanged.msg
            Lane.msg
            NDTStat.msg
            ObjLabel.msg
            ObjPose.msg
            ProjectionMatrix.msg
            VscanTracked.msg
            VscanTrackedArray.msg
            Waypoint.msg
            WaypointState.msg
            VehicleCmd.msg
            VehicleStatus.msg
            TrafficLightResult.msg
            TrafficLightResultArray.msg

            AccelCmd.msg
            AdjustXY.msg
            BrakeCmd.msg
            IndicatorCmd.msg
            LampCmd.msg
            SteerCmd.msg
            TrafficLight.msg
            StateCmd.msg
            State.msg
            ## Sync
            SyncTimeMonitor.msg
            SyncTimeDiff.msg

            ## Remote Control
            RemoteCmd.msg
)

## Generate added messages and services with any dependencies listed here
generate_messages(
        DEPENDENCIES
        std_msgs
        geometry_msgs
        sensor_msgs
        jsk_recognition_msgs
)

ament_export_dependencies(std_msgs)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(sensor_msgs)
ament_export_dependencies(jsk_recognition_msgs)

ament_package()
