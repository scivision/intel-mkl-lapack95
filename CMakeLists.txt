cmake_minimum_required(VERSION 3.20.2...3.24)

project(LAPACK95MKL LANGUAGES Fortran)

if(NOT DEFINED ENV{MKLROOT})
  message(FATAL_ERROR "MKL not detected via MKLROOT environment variable")
endif()

if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  message(FATAL_ERROR "Please specify the install prefix  via the -DCMAKE_INSTALL_PREFIX=<path> option")
endif()

cmake_path(CONVERT "$ENV{MKLROOT}" TO_CMAKE_PATH_LIST MKLROOT NORMALIZE)

message(STATUS "MKLROOT: ${MKLROOT}")

cmake_path(GET CMAKE_Fortran_COMPILER STEM FC)

message(STATUS "FC: ${FC}")

include(ExternalProject)

find_program(MAKE_EXECUTABLE
NAMES gmake mingw32-make make
NAMES_PER_DIR
DOC "GNU Make"
REQUIRED
)

find_path(src
NAMES makefile
HINTS $ENV{MKLROOT}
PATH_SUFFIXES interfaces/lapack95
NO_DEFAULT_PATH
REQUIRED
)

ExternalProject_Add(lapack95
SOURCE_DIR ${src}
CONFIGURE_HANDLED_BY_BUILD true
CONFIGURE_COMMAND ""
BUILD_COMMAND ${MAKE_EXECUTABLE} libintel64 interface=lp64 INSTALL_DIR=${CMAKE_INSTALL_PREFIX} FC=${FC} -j -C <SOURCE_DIR>
INSTALL_COMMAND ""
)
