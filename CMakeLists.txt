# ######################################################################################################################
# CMAKE
# ######################################################################################################################
message(STATUS "CMAKE_VERSION      =${CMAKE_VERSION}")
message(STATUS "CMAKE_COMMAND      =${CMAKE_COMMAND}")
message(STATUS "CMAKE_CTEST_COMMAND=${CMAKE_CTEST_COMMAND}")
message(STATUS "CMAKE_CPACK_COMMAND=${CMAKE_CPACK_COMMAND}")
message(STATUS "CMAKE_EDIT_COMMAND =${CMAKE_EDIT_COMMAND}")
message(STATUS "CMAKE_MAKE_PROGRAM =${CMAKE_MAKE_PROGRAM}")

# ######################################################################################################################
# 调试函数
# ######################################################################################################################
function ( print_all_variables )
  message ( STATUS )
  get_property ( _variables DIRECTORY PROPERTY VARIABLES )

  foreach ( _variable ${_variables} )
    message ( STATUS "    ${_variable}=${${_variable}}" )
  endforeach ()
endfunction ()

function ( print_all_variables_with_regex _regex )
  message ( STATUS )
  message ( STATUS ${_regex} )
  get_property ( _variables DIRECTORY PROPERTY VARIABLES )

  foreach ( _variable ${_variables} )
    if ( _variable MATCHES "${_regex}" )
      message ( STATUS "    ${_variable}=${${_variable}}" )
    endif ()
  endforeach ()
endfunction ()

# ######################################################################################################################
# 项目设置
# ######################################################################################################################
cmake_minimum_required(VERSION 3.16 FATAL_ERROR)
get_filename_component(PROJECT_FOLDER_PATH ${CMAKE_CURRENT_LIST_FILE} PATH)
get_filename_component(PROJECT_FOLDER_NAME ${PROJECT_FOLDER_PATH} NAME)
project(${PROJECT_FOLDER_NAME} LANGUAGES C CXX DESCRIPTION "examples for ${PROJECT_FOLDER_NAME}")

# ######################################################################################################################
# 编译参数
# ######################################################################################################################
# 指定C语言标准
# 90(C89/C90) 99(C99) 11(C11) 17(C17)[V3.21] 23(C23)[V3.21]
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED TRUE)
set(CMAKE_C_EXTENSIONS TRUE)

# 指定C++语言标准
# 98(C++98) 11(C++11) 14(C++14) 17(C++17)[V3.8] 20(C++20)[V3.12] 23(C++23)[V3.20] 26(C++26)[V3.25]
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)
set(CMAKE_CXX_EXTENSIONS TRUE)

set(CMAKE_POSITION_INDEPENDENT_CODE TRUE)
set(CMAKE_EXPORT_COMPILE_COMMANDS TRUE)
set(CMAKE_INCLUDE_CURRENT_DIR TRUE)

# 设置默认编译类型为RelWithDebInfo，即 -o2 -g
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  set(CMAKE_BUILD_TYPE RelWithDebInfo CACHE STRING "Choose the type of build." FORCE)
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

# ######################################################################################################################
# RPATH
# ######################################################################################################################
set(CMAKE_SKIP_RPATH OFF)
set(CMAKE_SKIP_BUILD_RPATH OFF)
set(CMAKE_SKIP_INSTALL_RPATH OFF)
set(CMAKE_CXX_LINK_FLAGS "-Wl,-disable-new-dtags")

# ######################################################################################################################
# 线程库
# ######################################################################################################################
find_package(Threads REQUIRED)

if(${Threads_FOUND})
  if(${ENABLE_CMAKE_DEBUG})
    print_all_variables_with_regex(THREAD)
  endif()

  link_libraries(Threads::Threads)
else()
  message(FATAL_ERROR "Can not found Threads")
endif()

# ######################################################################################################################
# Targets
# ######################################################################################################################
file(GLOB_RECURSE SRC_FILES ${CMAKE_SOURCE_DIR}/sources/*.cpp)

include_directories(${CMAKE_SOURCE_DIR}/include)
foreach(SRC_FILE ${SRC_FILES})
  get_filename_component(TARGET_NAME ${SRC_FILE} NAME_WE)
  add_executable(${TARGET_NAME} ${SRC_FILE})
endforeach()
