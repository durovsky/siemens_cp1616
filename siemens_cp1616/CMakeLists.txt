cmake_minimum_required(VERSION 2.8.3)
project(siemens_cp1616)

find_package(catkin REQUIRED
  COMPONENTS
    message_generation
    roscpp
    std_msgs
)

include_directories(include ${catkin_INCLUDE_DIRS})

add_service_files(
  FILES
    reset_alarm.srv
    set_alarm.srv
)

generate_messages(
  DEPENDENCIES
    std_msgs
)

catkin_package(
  CATKIN_DEPENDS
    message_runtime
    roscpp
    std_msgs
  INCLUDE_DIRS
    include
  LIBRARIES
    ${PROJECT_NAME}
)


add_library(
  ${PROJECT_NAME}
  src/io_controller.cpp
  src/io_controller_callbacks.cpp
  src/io_device.cpp
  src/io_device_callbacks.cpp
)
target_link_libraries(${PROJECT_NAME} yaml-cpp)

#IO Controller wrapper
add_executable(
  ${PROJECT_NAME}_io_controller_wrapper
  src/io_controller_wrapper.cpp)
target_link_libraries(
  ${PROJECT_NAME}_io_controller_wrapper
  ${PROJECT_NAME}
  ${catkin_LIBRARIES}
  libpniousr.a
  libl2interface.a
  pthread
)
set_target_properties(${PROJECT_NAME}_io_controller_wrapper
  PROPERTIES OUTPUT_NAME io_controller_wrapper
  PREFIX "")

#IO Device wrapper
add_executable(
  ${PROJECT_NAME}_io_device_wrapper
  src/io_device_wrapper.cpp)
add_dependencies(
  ${PROJECT_NAME}_io_device_wrapper
  siemens_cp1616_gencpp)
target_link_libraries(
  ${PROJECT_NAME}_io_device_wrapper
  ${PROJECT_NAME}
  ${catkin_LIBRARIES}
  libpniousr.a
  libl2interface.a
  pthread
)
set_target_properties(${PROJECT_NAME}_io_device_wrapper
  PROPERTIES OUTPUT_NAME io_device_wrapper
  PREFIX "")


install(
  TARGETS
    ${PROJECT_NAME}
    ${PROJECT_NAME}_io_controller_wrapper
    ${PROJECT_NAME}_io_device_wrapper
  RUNTIME
    DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  LIBRARY
    DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(
   DIRECTORY
     include/${PROJECT_NAME}/
   DESTINATION
     ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)