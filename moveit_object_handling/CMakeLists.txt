cmake_minimum_required(VERSION 2.8.3)
project(moveit_object_handling)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  sensor_msgs
  moveit_msgs
  roscpp
  shape_msgs
  geometric_shapes
  std_msgs
  object_msgs 
  eigen_conversions
  convenience_ros_functions
)

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  INCLUDE_DIRS include
  LIBRARIES moveit_grasped_object_handler moveit_collision_matrix_manipulator 
  CATKIN_DEPENDS geometry_msgs sensor_msgs moveit_msgs
      roscpp shape_msgs std_msgs eigen_conversions convenience_ros_functions
)

###########
## Build ##
###########

add_definitions(-std=c++11)
add_definitions(${convenience_ros_functions_DEFINITIONS})

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
    include
    ${catkin_INCLUDE_DIRS}
)

add_library(moveit_collision_matrix_manipulator
    src/MoveItHelpers.cpp
    src/MoveItCollisionMatrixManipulator.cpp
)

add_library(moveit_grasped_object_handler
    src/GraspedObjectHandler.cpp
)


add_executable(moveit_collisionobject_generator
    src/ObjectMessageGenerator.cpp
    src/object_message_generator_node.cpp
)

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
# add_dependencies(moveit_object_handling_node moveit_object_handling_generate_messages_cpp)
add_dependencies(moveit_collisionobject_generator ${catkin_EXPORTED_TARGETS})
add_dependencies(moveit_collision_matrix_manipulator ${catkin_EXPORTED_TARGETS})
add_dependencies(moveit_grasped_object_handler ${catkin_EXPORTED_TARGETS})


target_link_libraries(moveit_collisionobject_generator
    moveit_collision_matrix_manipulator
    ${catkin_LIBRARIES}
)

target_link_libraries(moveit_collision_matrix_manipulator
   ${catkin_LIBRARIES}
)
target_link_libraries(moveit_grasped_object_handler
   ${catkin_LIBRARIES}
)

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executables and/or libraries for installation
install(TARGETS moveit_grasped_object_handler moveit_collision_matrix_manipulator moveit_collisionobject_generator  
   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
   FILES_MATCHING PATTERN "*.h"
)

install(DIRECTORY launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
  FILES_MATCHING PATTERN "*.launch"
)

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_moveit_object_handling.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
