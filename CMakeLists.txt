cmake_minimum_required(VERSION 3.1)
project(tetgen VERSION 0.0.0 LANGUAGES CXX)

option(TETGEN_BUILD_EXECUTABLE "Build tetgen executable" OFF)
option(TETGEN_BUILD_LIBRARY    "Build libtetgen library" ON)

# config
set(exe_dest "bin")
set(incl_dest "include")
set(lib_dest "lib")
set(cfg_dest "${lib_dest}/cmake/${PROJECT_NAME}")
set(gen_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
set(version_config "${gen_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${gen_dir}/${PROJECT_NAME}Config.cmake")
set(TARGETS_EXPORT_NAME "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

# sources
set(CXX_HEADERS tetgen.h)
set(CXX_SOURCES tetgen.cxx predicates.cxx)

# flags / defs
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU" OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  set(TETGEN_OPTIMIZATION_FLAGS $<$<NOT:$<CONFIG:Debug>>:-O3>)
elseif(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
  set(TETGEN_OPTIMIZATION_FLAGS $<$<NOT:$<CONFIG:Debug>>:/O2>)
endif()


if(TETGEN_BUILD_LIBRARY)
  message("*** building tetgen library ***")
  add_library(tetgen STATIC ${CXX_SOURCES})
  add_library(tetgen::tetgen ALIAS tetgen)
  target_compile_definitions(tetgen PUBLIC "TETLIBRARY")
  # Generate position independent code
  set_target_properties(tetgen PROPERTIES POSITION_INDEPENDENT_CODE ON)
  target_include_directories(tetgen
    INTERFACE
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:${incl_dest}>)
  target_compile_options(tetgen PRIVATE ${TETGEN_OPTIMIZATION_FLAGS}) 

endif()

if(TETGEN_BUILD_EXECUTABLE)
  message("*** building tetgen executable ***")
  add_executable(tetgen_exec ${CXX_SOURCES})
  set_target_properties(tetgen_exec PROPERTIES OUTPUT_NAME tetgen)
  target_compile_options(tetgen_exec PRIVATE ${TETGEN_OPTIMIZATION_FLAGS}) 
endif()


message("*** applied optimization flags: ${TETGEN_OPTIMIZATION_FLAGS} ***")

# configure config files
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)
configure_package_config_file(
    "cmake/config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${cfg_dest}"
)

if(TETGEN_BUILD_LIBRARY)
  install(
      TARGETS tetgen
      EXPORT "${TARGETS_EXPORT_NAME}"
      LIBRARY DESTINATION ${lib_dest}
      ARCHIVE DESTINATION ${lib_dest}
      INCLUDES DESTINATION "${incl_dest}"
  )
endif()

if(TETGEN_BUILD_EXECUTABLE)
  install(
    TARGETS tetgen_exec
    EXPORT ${TARGETS_EXPORT_NAME}
    RUNTIME DESTINATION ${exec_dest}
  )
endif()

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${cfg_dest}"
)

install(
    EXPORT "${TARGETS_EXPORT_NAME}"
    NAMESPACE "${namespace}"
    DESTINATION "${cfg_dest}"
)
install(
    FILES ${CXX_HEADERS}
    DESTINATION ${incl_dest}
)
