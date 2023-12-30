cmake_minimum_required(VERSION 3.16)

project(proxmox_library VERSION 0.0.1 LANGUAGES C CXX)
set(CMAKE_CXX_STANDARD 20)
 
message(STATUS "START COMPILE PACKAGE_TEMPLATE")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")

set(proxmox_shared_dir "lib")
set(proxmox_exe_dir "bin")
set(prefix_shared_library "lib")
 

if (ANDROID)
 set(package_shared_extension "_${ANDROID_ARCH_NAME}.so")
 set(package_exe_extension "")
 set(package_name "proxmox_${ANDROID_ARCH_NAME}")
elseif (WIN32)
 set(package_shared_extension ".dll")
 set(package_exe_extension ".exe") 
 set(package_name "proxmox")
elseif (APPLE)
 set(package_shared_extension ".dylib")
 set(package_exe_extension "") 
 set(package_name "proxmox")
else()
 set(package_shared_extension ".so")
 set(package_exe_extension "") 
 set(package_name "proxmox")
endif()


add_library(proxmox_shared SHARED proxmox.cpp)
add_executable(proxmox_exe proxmox.cpp)

set_target_properties(proxmox_shared PROPERTIES 
  LIBRARY_OUTPUT_DIRECTORY "${proxmox_shared_dir}"
  PREFIX "${prefix_shared_library}"
  OUTPUT_NAME "${package_name}"
)

set_target_properties(proxmox_exe PROPERTIES 
  RUNTIME_OUTPUT_DIRECTORY "${proxmox_exe_dir}"
  OUTPUT_NAME "${package_name}"
)

if (WIN32)
 set_target_properties(proxmox_shared PROPERTIES
  WINDOWS_EXPORT_ALL_SYMBOLS ON
)
endif (WIN32)
 
add_custom_command(
  TARGET proxmox_shared POST_BUILD 
  COMMAND ${CMAKE_STRIP} --strip-debug --strip-unneeded "${proxmox_shared_dir}/${prefix_shared_library}proxmox${package_shared_extension}"
)

add_custom_command(
  TARGET proxmox_exe POST_BUILD 
  COMMAND ${CMAKE_STRIP} --strip-debug --strip-unneeded "${proxmox_exe_dir}/proxmox${package_exe_extension}" 
)

 