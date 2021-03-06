#=============================================================================
#   CMake build system files
#
#   Copyright (c) 2014 pocl developers
#
#   Permission is hereby granted, free of charge, to any person obtaining a copy
#   of this software and associated documentation files (the "Software"), to deal
#   in the Software without restriction, including without limitation the rights
#   to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
#   copies of the Software, and to permit persons to whom the Software is
#   furnished to do so, subject to the following conditions:
#
#   The above copyright notice and this permission notice shall be included in
#   all copies or substantial portions of the Software.
#
#   THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
#   IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
#   FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
#   AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
#   LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
#   OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
#   THE SOFTWARE.
#
#=============================================================================

add_subdirectory("pthread")
add_subdirectory("basic")
add_subdirectory("topology")
set(POCL_DEVICES_LINK_LIST ${Hwloc_LDFLAGS})

set(POCL_DEVICES_OBJS "$<TARGET_OBJECTS:pocl-devices>"
      "$<TARGET_OBJECTS:pocl-devices-pthread>"
      "$<TARGET_OBJECTS:pocl-devices-basic>"
      "$<TARGET_OBJECTS:pocl-devices-topology>")

if(ENABLE_TCE)
  include_directories(AFTER "tce")
  add_subdirectory("tce")
  list(APPEND POCL_DEVICES_LINK_LIST ${TCE_LIBS})
endif()

if(ENABLE_HSA)
  include_directories(AFTER "${HSA_INCLUDES}")
  add_subdirectory("hsa")
  list(APPEND POCL_DEVICES_LINK_LIST ${HSALIB})
endif()

set(POCL_DEVICES_SOURCES 
  devices.h  devices.c
  bufalloc.c  dev_image.h
  common.h common.c
  bufalloc.h  cpuinfo.c cpuinfo.h)

if(MSVC)
  set_source_files_properties( ${POCL_DEVICES_SOURCES} PROPERTIES LANGUAGE CXX )
endif(MSVC)
add_library("pocl-devices" OBJECT ${POCL_DEVICES_SOURCES})

set(POCL_DEVICES_LINK_LIST ${POCL_DEVICES_LINK_LIST} PARENT_SCOPE)
set(POCL_DEVICES_OBJS ${POCL_DEVICES_OBJS} PARENT_SCOPE)
