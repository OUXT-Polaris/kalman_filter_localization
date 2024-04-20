cmake_minimum_required(VERSION 3.5)
project(kalman_filter_localization)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  #add_compile_options(-Wall -Wextra -Wpedantic)
endif()

install(
  DIRECTORY launch param
  DESTINATION share/${PROJECT_NAME}
)

# find dependencies
find_package(ament_cmake_auto REQUIRED)
find_package(Eigen3 REQUIRED)
ament_auto_find_build_dependencies()

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_auto_add_library(ekf_localization_component SHARED
src/ekf_localization_component.cpp
)

target_compile_definitions(ekf_localization_component PRIVATE "KFL_EKFL_BUILDING_DLL")
ament_target_dependencies(ekf_localization_component
  rclcpp rclcpp_components nav_msgs sensor_msgs tf2 tf2_eigen tf2_geometry_msgs)

add_executable(ekf_localization_node
src/ekf_localization_node.cpp
)

target_link_libraries(ekf_localization_node
ekf_localization_component)

ament_target_dependencies(ekf_localization_node
  rclcpp rclcpp_components nav_msgs sensor_msgs tf2 tf2_eigen tf2_geometry_msgs)

include_directories(
  include
  ${EIGEN3_INCLUDE_DIRS}
)

rclcpp_components_register_nodes(ekf_localization_component
  "kalman_filter_localization::EkfLocalizationComponent")

install(
  DIRECTORY launch param
  DESTINATION share/${PROJECT_NAME}
)

ament_auto_package()
