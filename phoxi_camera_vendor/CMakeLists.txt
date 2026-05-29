cmake_minimum_required(VERSION 3.22)
project(phoxi_camera_vendor)

find_package(ament_cmake REQUIRED)

if(DEFINED ENV{PHOXI_CONTROL_PATH} AND NOT "$ENV{PHOXI_CONTROL_PATH}" STREQUAL "")
  set(_phoxi_path "$ENV{PHOXI_CONTROL_PATH}")
elseif(DEFINED PhoXi_DIR)
  set(_phoxi_path "${PhoXi_DIR}")
else()
  file(GLOB _phoxi_candidates "/opt/Photoneo/PhoXiControl-*")
  list(SORT _phoxi_candidates COMPARE NATURAL ORDER DESCENDING)
  list(GET _phoxi_candidates 0 _phoxi_path)
endif()

find_package(PhoXi REQUIRED CONFIG PATHS "${_phoxi_path}")

set(PHOXI_INSTALL_PATH "${_phoxi_path}")
configure_file(
  cmake/phoxi_camera_vendor_phoxi_dir-extras.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/cmake/phoxi_camera_vendor_phoxi_dir-extras.cmake
  @ONLY
)

add_library(${PROJECT_NAME} INTERFACE)
target_link_libraries(${PROJECT_NAME} INTERFACE phoxi::phoxi)

install(
  TARGETS ${PROJECT_NAME}
  EXPORT export_${PROJECT_NAME}
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
)

ament_export_targets(export_${PROJECT_NAME})
ament_export_dependencies(PhoXi)

ament_package(
  CONFIG_EXTRAS "${CMAKE_CURRENT_BINARY_DIR}/cmake/phoxi_camera_vendor_phoxi_dir-extras.cmake"
)

