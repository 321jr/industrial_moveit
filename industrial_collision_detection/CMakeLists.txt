cmake_minimum_required(VERSION 2.8.3)
project(industrial_collision_detection)

add_definitions("-std=c++11")

find_package(Eigen3 REQUIRED)
find_package(Boost REQUIRED)
find_package(console_bridge REQUIRED)

find_package(PkgConfig REQUIRED)
pkg_check_modules(LIBFCL REQUIRED fcl)
find_library(LIBFCL_LIBRARIES_FULL ${LIBFCL_LIBRARIES} ${LIBFCL_LIBRARY_DIRS})
set(LIBFCL_LIBRARIES "${LIBFCL_LIBRARIES_FULL}")

find_package(catkin REQUIRED COMPONENTS
  roscpp
  cmake_modules
  pluginlib
  moveit_core
)

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
 INCLUDE_DIRS include ${catkin_INCLUDE_DIRS}
 LIBRARIES
   ${PROJECT_NAME}
 CATKIN_DEPENDS roscpp moveit_core cmake_modules pluginlib
 DEPENDS Boost EIGEN3 LIBFCL
)

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(include
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
  ${LIBFCL_INCLUDE_DIRS}
)

add_library(${PROJECT_NAME}
  src/collision_detection/collision_common.cpp
  src/collision_detection/collision_robot_industrial.cpp
  src/collision_detection/collision_world_industrial.cpp
)
target_link_libraries(${PROJECT_NAME}
  ${catkin_LIBRARIES}
  ${console_bridge_LIBRARIES}
  ${urdfdom_LIBRARIES}
  ${urdfdom_headers_LIBRARIES}
  ${LIBFCL_LIBRARIES}
  ${Boost_LIBRARIES})

add_library(${PROJECT_NAME}_plugin
  src/industrial_collision_detection_plugin.cpp
)
target_link_libraries(${PROJECT_NAME}_plugin ${PROJECT_NAME} ${catkin_LIBRARIES})

#############
## Install ##
#############
install(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}_plugin LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})
install(DIRECTORY include/${PROJECT_NAME}/ DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})
install(
  FILES
    industrial_collision_detection_plugin_description.xml
  DESTINATION
    ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
