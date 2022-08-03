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

find_file(src
NAMES makefile
HINTS $ENV{MKLROOT}
PATH_SUFFIXES interfaces/lapack95
NO_DEFAULT_PATH
REQUIRED
)

cmake_path(GET src PARENT_PATH srcdir)

if(WIN32)
  set(names nmake)
  set(opts install_dir=${CMAKE_INSTALL_PREFIX})
else()
  set(names gmake make)
  set(opts INSTALL_DIR=${CMAKE_INSTALL_PREFIX})
endif()

find_program(MAKE_EXECUTABLE
NAMES ${names}
NAMES_PER_DIR
DOC "Make"
REQUIRED
)

message(STATUS "MAKE_EXECUTABLE: ${MAKE_EXECUTABLE}")

ExternalProject_Add(lapack95
SOURCE_DIR ${srcdir}
BINARY_DIR ${srcdir}
CONFIGURE_HANDLED_BY_BUILD true
CONFIGURE_COMMAND ""
BUILD_COMMAND ${MAKE_EXECUTABLE} libintel64 interface=lp64 FC=${FC} -f ${src} ${opts}
INSTALL_COMMAND ""
)
