cmake_minimum_required(VERSION 3.16...3.28)
project(rigid_pose_broadcaster)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 20)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra)
endif()

find_package(ament_cmake REQUIRED)
find_package(controller_interface REQUIRED)
find_package(pluginlib REQUIRED)
find_package(rclcpp_lifecycle REQUIRED)
find_package(rcutils REQUIRED)
find_package(realtime_tools REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(ndi_msgs REQUIRED)
find_package(eigen3_cmake_module REQUIRED)
find_package(Eigen3)

add_library(rigid_pose_broadcaster
  SHARED
  src/rigid_pose_broadcaster.cpp
)
target_include_directories(rigid_pose_broadcaster PRIVATE include)
# ament_target_dependencies() was removed in ROS 2 Lyric (ament_cmake >= 2.8);
# link against the modern namespaced imported targets instead.
target_link_libraries(rigid_pose_broadcaster
  PUBLIC
    builtin_interfaces::builtin_interfaces
    controller_interface::controller_interface
    pluginlib::pluginlib
    rclcpp_lifecycle::rclcpp_lifecycle
    rcutils::rcutils
    realtime_tools::realtime_tools
    geometry_msgs::geometry_msgs
    Eigen3::Eigen
    ndi_msgs::ndi_msgs
)
# Causes the visibility macros to use dllexport rather than dllimport,
# which is appropriate when building the dll but not consuming it.
target_compile_definitions(rigid_pose_broadcaster PRIVATE "TRACKER_POSE_BROADCASTER_BUILDING_DLL")
# prevent pluginlib from using boost
target_compile_definitions(rigid_pose_broadcaster PUBLIC "PLUGINLIB__DISABLE_BOOST_FUNCTIONS")
pluginlib_export_plugin_description_file(controller_interface controller_plugin.xml)

install(
  DIRECTORY include/
  DESTINATION include
)

install(
  TARGETS
  rigid_pose_broadcaster
  RUNTIME DESTINATION bin
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
)

ament_export_dependencies(
  controller_interface
  rclcpp_lifecycle
  geometry_msgs
  eigen3_cmake_module
  Eigen3
  ndi_msgs
)
ament_export_include_directories(
  include
)
ament_export_libraries(
  rigid_pose_broadcaster
)
ament_package()
