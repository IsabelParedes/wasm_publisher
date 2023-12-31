cmake_minimum_required(VERSION 3.8)
project(wasm_publisher)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
# uncomment the following section in order to fill in
# further dependencies manually.
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(example_interfaces REQUIRED)

add_executable(talker src/publisher.cpp)
ament_target_dependencies(talker rclcpp std_msgs)

add_compile_options("SHELL:-s USE_PTHREADS")

add_link_options("SHELL:-s USE_PTHREADS")

# if(DEFINED ENV{EMSDK})
set(CMAKE_EXECUTABLE_SUFFIX ".html")

target_compile_options(talker
  PUBLIC "SHELL: -fexceptions"
)

target_link_options(talker
  PUBLIC "SHELL: -lembind"
  PUBLIC "SHELL: --pre-js ${CMAKE_SOURCE_DIR}/src/pre.js"
  PUBLIC "SHELL: -fexceptions"
  PUBLIC "SHELL: -s ASYNCIFY=1"
  PUBLIC "SHELL: -s ALLOW_MEMORY_GROWTH=1"
  PUBLIC "SHELL: -s DEMANGLE_SUPPORT=1"
  PUBLIC "SHELL: -s ASSERTIONS=1"
  PUBLIC "SHELL: -s EXIT_RUNTIME=1"
  PUBLIC "SHELL: -s WASM=1"
  PUBLIC "SHELL: -s USE_PTHREADS=0"
  PUBLIC "SHELL: -s TOTAL_STACK=16mb"
  PUBLIC "SHELL: -s INITIAL_MEMORY=64mb"
  PUBLIC "SHELL: -s ASYNCIFY_STACK_SIZE=24576"
)

install(
  TARGETS talker
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()
