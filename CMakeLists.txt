cmake_minimum_required(VERSION 3.8)
project(nuitrack_body_tracker)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# For nuitrack
# add_compile_options(-std=c++11 -D_GLIBCXX_USE_CXX11_ABI=0)
set(NUITRACK_SDK_PATH /home/kjs/nuitrack-sdk) 

set(THIS_PACKAGE_INCLUDE_DEPENDS
  ament_cmake
  rclcpp
  geometry_msgs
  visualization_msgs
  body_tracker_msgs
  std_msgs
)

foreach(Dependency IN ITEMS ${THIS_PACKAGE_INCLUDE_DEPENDS})
  find_package(${Dependency} REQUIRED)
endforeach()


include_directories(
  ${NUITRACK_SDK_PATH}/Nuitrack/include   
  ${NUITRACK_SDK_PATH}/Nuitrack/include/middleware  
  ${catkin_INCLUDE_DIRS}
)

add_executable(${PROJECT_NAME}_node src/nuitrack_body_tracker_node.cpp)
ament_target_dependencies(${PROJECT_NAME}_node ${THIS_PACKAGE_INCLUDE_DEPENDS})

target_include_directories(${PROJECT_NAME}_node PUBLIC
  ${NUITRACK_SDK_PATH}/Nuitrack/include   
  ${NUITRACK_SDK_PATH}/Nuitrack/include/middleware  
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

target_link_libraries(
  ${PROJECT_NAME}_node
  ${NUITRACK_SDK_PATH}/Nuitrack/lib/linux64/libnuitrack.so
  ${NUITRACK_SDK_PATH}/Nuitrack/lib/linux64/libmiddleware.so
  ${catkin_LIBRARIES}
)

install(TARGETS ${PROJECT_NAME}_node
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()