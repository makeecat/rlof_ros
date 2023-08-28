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
        LIBRARIES rlof_ros_lib
    )

    include_directories(
        ${catkin_INCLUDE_DIRS}
    )
    # Set link libraries used by all binaries
    list(APPEND thirdparty_libraries
        # ${Boost_LIBRARIES}
        ${OpenCV_LIBRARIES}
        ${catkin_LIBRARIES}
    )
    list(APPEND LIBRARY_SOURCES
        optflow/src/rlof/berlof_invoker.hpp
        optflow/src/rlof/geo_interpolation.cpp
        optflow/src/rlof/geo_interpolation.hpp
        optflow/src/rlof/plk_invoker.hpp
        optflow/src/rlof/rlof_invoker.hpp
        optflow/src/rlof/rlof_invokerbase.hpp
        optflow/src/rlof/rlof_localflow.cpp
        optflow/src/rlof/rlof_localflow.h
        optflow/src/rlofflow.cpp
    )

    # Declare a C++ library
    add_library(rlof_ros_lib SHARED
        ${LIBRARY_SOURCES}
    )
    target_link_libraries(rlof_ros_lib ${thirdparty_libraries})
    target_include_directories(rlof_ros_lib PUBLIC optflow/src optflow/include)
    install(TARGETS rlof_ros_lib
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    )
    install(DIRECTORY optflow/src/ optflow/include/
            DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
            FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp"
    )
 elseif (ament_cmake_FOUND)
    message(STATUS "ROS *2* version found")
    ament_package()
 else ()
     message(STATUS "No ROS versions found, doing nothing...")
 endif ()