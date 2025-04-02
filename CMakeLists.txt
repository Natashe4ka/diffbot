# cmake_minimum_required(VERSION 3.16)
# project(diffbot LANGUAGES CXX)

# # find_package(ros2_control_cmake REQUIRED)
# # set_compiler_options()
# # export_windows_symbols()

# # find dependencies
# set(THIS_PACKAGE_INCLUDE_DEPENDS
#   hardware_interface
#   pluginlib
#   rclcpp
#   rclcpp_lifecycle
# )

# # Specify the required version of ros2_control
# find_package(controller_manager 4.0.0)
# # Handle the case where the required version is not found
# if(NOT controller_manager_FOUND)
#   message(FATAL_ERROR "ros2_control version 4.0.0 or higher is required. "
#   "Are you using the correct branch of the ros2_control_demos repository?")
# endif()

# # find dependencies
# find_package(backward_ros REQUIRED)
# find_package(ament_cmake REQUIRED)
# foreach(Dependency IN ITEMS ${THIS_PACKAGE_INCLUDE_DEPENDS})
#   find_package(${Dependency} REQUIRED)
# endforeach()

# ## COMPILE
# add_library(
#   diffbot
#   SHARED
#   hardware/diffbot_system.cpp
# )
# target_compile_features(diffbot PUBLIC cxx_std_17)
# target_include_directories(diffbot PUBLIC
# $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/hardware/include>
# $<INSTALL_INTERFACE:include/diffbot>
# )
# ament_target_dependencies(
#   diffbot PUBLIC
#   ${THIS_PACKAGE_INCLUDE_DEPENDS}
# )

# # Export hardware plugins
# pluginlib_export_plugin_description_file(hardware_interface diffbot.xml)

# # INSTALL
# install(
#   DIRECTORY hardware/include/
#   DESTINATION include/diffbot
# )
# install(
#   DIRECTORY description/launch description/ros2_control description/urdf
#   DESTINATION share/diffbot
# )
# install(
#   DIRECTORY config
#   DESTINATION share/diffbot
# )
# install(TARGETS diffbot
#   EXPORT export_diffbot
#   ARCHIVE DESTINATION lib
#   LIBRARY DESTINATION lib
#   RUNTIME DESTINATION bin
# )

# # if(BUILD_TESTING)
# #   find_package(ament_cmake_pytest REQUIRED)

# #   ament_add_pytest_test(example_2_urdf_xacro test/test_urdf_xacro.py)
# #   ament_add_pytest_test(view_example_2_launch test/test_view_robot_launch.py)
# #   ament_add_pytest_test(run_example_2_launch test/test_diffbot_launch.py)
# # endif()

# ## EXPORTS
# ament_export_targets(export_diffbot HAS_LIBRARY_TARGET)
# ament_export_dependencies(${THIS_PACKAGE_INCLUDE_DEPENDS})
# ament_package()


cmake_minimum_required(VERSION 3.22)
project(diffbot)

find_package(ament_cmake REQUIRED)


if(CMAKE_CXX_COMPILER_ID MATCHES "(GNU|Clang)")
  add_compile_options(-Wall -Wextra)
endif()

set(PACKAGE_INCLUDE_DEPENDS
   hardware_interface
  controller_interface
  realtime_tools
 # semantic_components
  sensor_msgs
  pluginlib
  rclcpp
  rclcpp_lifecycle
  moveit_ros_planning_interface
)

find_package(ament_cmake REQUIRED)
find_package(moveit_ros_planning_interface REQUIRED)
find_package(rclcpp REQUIRED)

foreach(Dependency IN ITEMS ${PACKAGE_INCLUDE_DEPENDS})
  find_package(${Dependency} REQUIRED)
endforeach()

add_library(
  diffbot
  SHARED
  hardware/diffbot_hardware.cpp
  # hardware/ruka_sensor_hardware.cpp
 
)

target_compile_features(diffbot PUBLIC cxx_std_17)
target_include_directories(diffbot PUBLIC
$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/hardware/include>

$<INSTALL_INTERFACE:include/diffbot>
)
ament_target_dependencies(
  diffbot PUBLIC
  ${PACKAGE_INCLUDE_DEPENDS}
 
)

target_compile_definitions(${PROJECT_NAME} PRIVATE "DIFFBOT_BUILDING_DLL")

pluginlib_export_plugin_description_file(hardware_interface diffbot.xml)

install(DIRECTORY launch DESTINATION share/${PROJECT_NAME}
  # PATTERN "setup_assistant.launch" EXCLUDE
  )
install(DIRECTORY config DESTINATION share/${PROJECT_NAME})
# install(FILES .setup_assistant DESTINATION share/${PROJECT_NAME})


install(
  DIRECTORY hardware/include/
  DESTINATION include/diffbot
)



install(TARGETS diffbot
  EXPORT export_diffbot
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

if(BUILD_TESTING)
  find_package(ament_cmake_gtest REQUIRED)
endif()

## EXPORTS
ament_export_targets(export_diffbot HAS_LIBRARY_TARGET)
ament_export_dependencies(${PACKAGE_INCLUDE_DEPENDS})




ament_package()
