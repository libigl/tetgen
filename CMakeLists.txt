cmake_minimum_required(VERSION 3.1)
project(tetgen)

option(BUILD_EXECUTABLE "Build tetgen executable" OFF)
option(BUILD_LIBRARY    "Build libtetgen library" ON)

if(BUILD_LIBRARY)
  add_library(tetgen STATIC tetgen.cxx predicates.cxx)
  target_compile_definitions(tetgen PRIVATE -DTETLIBRARY)
  # Generate position independent code
  set_target_properties(tetgen PROPERTIES POSITION_INDEPENDENT_CODE ON)
endif()


if(BUILD_EXECUTABLE)
  add_executable(tetgen_exec tetgen.cxx predicates.cxx)
  set_target_properties(tetgen_exec PROPERTIES OUTPUT_NAME tetgen)
endif()
