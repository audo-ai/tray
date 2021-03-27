cmake_minimum_required(VERSION 3.10)

project(tray)

add_library(tray INTERFACE)

if (${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
  include(FindPkgConfig)
  pkg_search_module(APPINDICATOR REQUIRED appindicator3-0.1)

  target_compile_definitions(tray INTERFACE TRAY_APPINDICATOR=1)
  target_include_directories(tray INTERFACE ${APPINDICATOR_INCLUDE_DIRS})
  target_link_libraries(tray INTERFACE ${APPINDICATOR_LIBRARIES})

  target_include_directories(tray INTERFACE ./)
elseif (${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
  target_compile_definitions(tray INTERFACE TRAY_WINAPI=1)

  target_include_directories(tray INTERFACE ./)
elseif (${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
  target_compile_definitions(tray INTERFACE TRAY_APPKIT=1)
  target_link_libraries(tray INTERFACE Cocoa)

  target_include_directories(tray INTERFACE ./)
else()
  message(FATAL_ERROR "Platform not recognized (neither Linux nor Windows nor Darwin)")
endif()

add_executable(example example.c)
target_link_libraries(example tray)
