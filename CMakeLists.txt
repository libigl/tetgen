cmake_minimum_required(VERSION 2.6)
project(tetgen)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DTETLIBRARY")

add_library(tetgen STATIC tetgen.cxx predicates.cxx)
