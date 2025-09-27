cmake_minimum_required(VERSION 3.20)
project(Doku LANGUAGES CXX)

# Components managed by this umbrella library
set(DOKU_COMPONENTS Logger StringUtils)
set(DOKU_COMPONENT_Logger_DIR external/Logger)
set(DOKU_COMPONENT_StringUtils_DIR external/StringUtils)

function(doku_configure_component component_name)
  string(TOUPPER "${component_name}" component_upper)

  set(enable_var "${component_upper}_ENABLE_INSTALL")
  set(${enable_var} ${DOKU_ENABLE_INSTALL}
      CACHE BOOL "Install ${component_name} when building Doku" FORCE)

  if(DOKU_ENABLE_INSTALL)
    set(export_set_var "${component_upper}_EXPORT_SET")
    set(export_file_var "${component_upper}_INSTALL_EXPORT_FILE")

    set(${export_set_var} DokuTargets
        CACHE STRING "Export set used when installing ${component_name}" FORCE)
    set(${export_file_var} OFF
        CACHE BOOL "Install the ${component_name} export file" FORCE)
  endif()
endfunction()

set(DOKU_IS_TOP_LEVEL OFF)
if(CMAKE_SOURCE_DIR STREQUAL PROJECT_SOURCE_DIR)
  set(DOKU_IS_TOP_LEVEL ON)
endif()

set(_doku_default_install OFF)
if(DOKU_IS_TOP_LEVEL)
  set(_doku_default_install ON)
endif()
option(DOKU_ENABLE_INSTALL "Generate install/export targets for Doku" ${_doku_default_install})
unset(_doku_default_install)

foreach(component IN LISTS DOKU_COMPONENTS)
  doku_configure_component("${component}")
endforeach()

foreach(component IN LISTS DOKU_COMPONENTS)
  set(component_dir "${DOKU_COMPONENT_${component}_DIR}")
  add_subdirectory("${component_dir}")
endforeach()

add_library(Doku INTERFACE)
target_link_libraries(Doku INTERFACE Logger StringUtils)
target_include_directories(Doku INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>)

if(DOKU_ENABLE_INSTALL)
  include(GNUInstallDirs)

  install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
  install(TARGETS Doku EXPORT DokuTargets)
  install(EXPORT DokuTargets NAMESPACE Doku:: DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/Doku)
endif()

