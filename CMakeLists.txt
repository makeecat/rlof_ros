 cmake_minimum_required(VERSION 3.3)
 project(rlof_ros)

 # Find our ROS version!
 # NOTE: Default to using the ROS1 package if both are in our enviroment
 # NOTE: https://github.com/romainreignier/share_ros1_ros2_lib_demo
 find_package(catkin QUIET COMPONENTS roscpp)
 find_package(ament_cmake QUIET)
 find_package(OpenCV 4 REQUIRED)
 message(STATUS "OPENCV: " ${OpenCV_VERSION})
 if (catkin_FOUND)
    message(STATUS "ROS *1* version found")
    find_package(catkin REQUIRED COMPONENTS cv_bridge)
    catkin_package(
        CATKIN_DEPENDS cv_bridge
        INCLUDE_DIRS optflow/src optflow/include
        LIBRARIES rlof_ros
    )

    include_directories(
        ${catkin_INCLUDE_DIRS}
    )

    list(APPEND LIBRARY_SOURCES
        optflow/src/rlof/berlof_invoker.hpp
        optflow/src/rlof/rlof_localflow.cpp
        optflow/src/rlof/rlof_localflow.h
        optflow/src/rlof/rlof_invoker.hpp
        optflow/src/rlof/rlof_invokerbase.hpp
        optflow/src/rlof/geo_interpolation.cpp
        optflow/src/rlof/rlof_localflow.cpp
        optflow/src/rlof/rlof_localflow.h
    )

    # Declare a C++ library
    add_library(${PROJECT_NAME} SHARED
        ${LIBRARY_SOURCES}
        
    )
 elseif (ament_cmake_FOUND)
    message(STATUS "ROS *2* version found")
    ament_package()
 else ()
     message(STATUS "No ROS versions found, doing nothing...")
 endif ()