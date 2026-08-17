cmake_minimum_required(VERSION 3.16)
project(btcpp_ros2_samples)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)


find_package(ament_cmake REQUIRED)
find_package(behaviortree_ros2 REQUIRED)
find_package(btcpp_ros2_interfaces REQUIRED)
find_package(std_msgs REQUIRED)
find_package(std_srvs REQUIRED)

set(THIS_PACKAGE_DEPS
    behaviortree_ros2
    std_msgs
    std_srvs
    btcpp_ros2_interfaces )

######################################################
# Simple example showing how to use and customize the BtExecutionServer
add_executable(sample_bt_executor src/sample_bt_executor.cpp)
foreach(_dep IN ITEMS ${THIS_PACKAGE_DEPS})
  if(${_dep}_TARGETS)
    target_link_libraries(sample_bt_executor PUBLIC ${${_dep}_TARGETS})
  else()
    target_link_libraries(sample_bt_executor PUBLIC ${${_dep}_LIBRARIES})
    target_include_directories(sample_bt_executor SYSTEM PUBLIC ${${_dep}_INCLUDE_DIRS})
  endif()
endforeach()

######################################################
# Build an Action Client that calls the sleep action (STATIC version)

add_executable(sleep_client
  src/sleep_action.cpp
  src/sleep_client.cpp)
foreach(_dep IN ITEMS ${THIS_PACKAGE_DEPS})
  if(${_dep}_TARGETS)
    target_link_libraries(sleep_client PUBLIC ${${_dep}_TARGETS})
  else()
    target_link_libraries(sleep_client PUBLIC ${${_dep}_LIBRARIES})
    target_include_directories(sleep_client SYSTEM PUBLIC ${${_dep}_INCLUDE_DIRS})
  endif()
endforeach()

######################################################
# Build a client that call the sleep action (Plugin version)

add_library(sleep_plugin SHARED src/sleep_action.cpp)
target_compile_definitions(sleep_plugin PRIVATE  BT_PLUGIN_EXPORT )
foreach(_dep IN ITEMS ${THIS_PACKAGE_DEPS})
  if(${_dep}_TARGETS)
    target_link_libraries(sleep_plugin PUBLIC ${${_dep}_TARGETS})
  else()
    target_link_libraries(sleep_plugin PUBLIC ${${_dep}_LIBRARIES})
    target_include_directories(sleep_plugin SYSTEM PUBLIC ${${_dep}_INCLUDE_DIRS})
  endif()
endforeach()

add_executable(sleep_client_dyn src/sleep_client.cpp)
target_compile_definitions(sleep_client_dyn PRIVATE USE_SLEEP_PLUGIN )
target_link_libraries(sleep_client_dyn PUBLIC sleep_plugin)
foreach(_dep IN ITEMS ${THIS_PACKAGE_DEPS})
  if(${_dep}_TARGETS)
    target_link_libraries(sleep_client_dyn PUBLIC ${${_dep}_TARGETS})
  else()
    target_link_libraries(sleep_client_dyn PUBLIC ${${_dep}_LIBRARIES})
    target_include_directories(sleep_client_dyn SYSTEM PUBLIC ${${_dep}_INCLUDE_DIRS})
  endif()
endforeach()

######################################################
# Build Server
add_executable(sleep_server src/sleep_server.cpp)
foreach(_dep IN ITEMS ${THIS_PACKAGE_DEPS})
  if(${_dep}_TARGETS)
    target_link_libraries(sleep_server PUBLIC ${${_dep}_TARGETS})
  else()
    target_link_libraries(sleep_server PUBLIC ${${_dep}_LIBRARIES})
    target_include_directories(sleep_server SYSTEM PUBLIC ${${_dep}_INCLUDE_DIRS})
  endif()
endforeach()

######################################################
# Build subscriber_test
add_executable(subscriber_test src/subscriber_test.cpp)
foreach(_dep IN ITEMS ${THIS_PACKAGE_DEPS})
  if(${_dep}_TARGETS)
    target_link_libraries(subscriber_test PUBLIC ${${_dep}_TARGETS})
  else()
    target_link_libraries(subscriber_test PUBLIC ${${_dep}_LIBRARIES})
    target_include_directories(subscriber_test SYSTEM PUBLIC ${${_dep}_INCLUDE_DIRS})
  endif()
endforeach()

######################################################
# the SetBool test
add_executable(bool_client src/bool_client.cpp src/set_bool_node.cpp)
foreach(_dep IN ITEMS ${THIS_PACKAGE_DEPS})
  if(${_dep}_TARGETS)
    target_link_libraries(bool_client PUBLIC ${${_dep}_TARGETS})
  else()
    target_link_libraries(bool_client PUBLIC ${${_dep}_LIBRARIES})
    target_include_directories(bool_client SYSTEM PUBLIC ${${_dep}_INCLUDE_DIRS})
  endif()
endforeach()

add_executable(bool_server src/bool_server.cpp )
foreach(_dep IN ITEMS ${THIS_PACKAGE_DEPS})
  if(${_dep}_TARGETS)
    target_link_libraries(bool_server PUBLIC ${${_dep}_TARGETS})
  else()
    target_link_libraries(bool_server PUBLIC ${${_dep}_LIBRARIES})
    target_include_directories(bool_server SYSTEM PUBLIC ${${_dep}_INCLUDE_DIRS})
  endif()
endforeach()

######################################################
# INSTALL

install(TARGETS
  sleep_client
  sleep_client_dyn
  sleep_server
  sleep_plugin
  subscriber_test
  sample_bt_executor
  bool_client
  bool_server
  DESTINATION lib/${PROJECT_NAME}
  )

######################################################
# INSTALL plugins for other packages to load

install(TARGETS
  sleep_plugin
  LIBRARY DESTINATION share/${PROJECT_NAME}/bt_plugins
  ARCHIVE DESTINATION share/${PROJECT_NAME}/bt_plugins
  RUNTIME DESTINATION share/${PROJECT_NAME}/bt_plugins
  )

######################################################
# INSTALL Behavior.xml's, ROS config and launch files

install(DIRECTORY
    behavior_trees
    config
    launch
    DESTINATION share/${PROJECT_NAME}/
    )


ament_export_dependencies(behaviortree_ros2 btcpp_ros2_interfaces)

ament_package()
