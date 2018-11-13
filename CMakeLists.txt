cmake_minimum_required(VERSION 2.8.12)
project(tetgen)

add_library(tetgen STATIC tetgen.cxx predicates.cxx)
target_compile_definitions(tetgen PRIVATE -DTETLIBRARY)

# Generate position independent code
set_target_properties(tetgen PROPERTIES POSITION_INDEPENDENT_CODE ON)
