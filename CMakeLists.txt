cmake_minimum_required(VERSION 3.5)
project(robot_localization_ranges)

set(CMAKE_CXX_STANDARD 17)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

set(CMAKE_BUILD_TYPE Release)

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(urdf REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(robot_localization REQUIRED)
find_package(anchor_msgs REQUIRED)

find_package(Eigen3 REQUIRED)

add_executable(ekf
    src/ekf_node.cpp 
    src/ros_filter_ranges.cpp 
    src/ekf.cpp)
    
target_include_directories(ekf PRIVATE include robot_localization-release/include ${EIGEN3_INCLUDE_DIRS})
ament_target_dependencies(ekf rclcpp sensor_msgs geometry_msgs nav_msgs tf2_ros urdf anchor_msgs robot_localization)

# explicitely link against rl_lib from robot_localization
find_library(rl_lib rl_lib)
target_link_libraries(ekf ${rl_lib})


if(NOT WIN32)
  ament_environment_hooks(
    "${ament_cmake_package_templates_ENVIRONMENT_HOOK_LIBRARY_PATH}"
    )
endif()

install(TARGETS ekf
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME})

ament_package()
