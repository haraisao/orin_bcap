#
#
cmake_minimum_required(VERSION 3.5)
project(orin_bcap)

#add_compile_options( -Wno-write-strings -fcompare-debug-second -g )
add_compile_options( -Wno-write-strings -fcompare-debug-second)
string(REPLACE ":" ";" CMAKE_PREFIX_PATH $ENV{AMENT_PREFIX_PATH})

option  (_USE_LINUX_API      "Use Linux API"   ON)
message (STATUS "_USE_LINUX_API=${_USE_LINUX_API}")
option (BUILD_SHARED_LIBS "Build as a shared library" ON)

set(_DN_USE_VARIANT_API ON)
set(_DN_USE_BSTR_API ON)

message (STATUS "_DN_USE_VARIANT_API=${_DN_USE_VARIANT_API}")
message (STATUS "_DN_USE_BSTR_API=${_DN_USE_BSTR_API}")
message (STATUS "BUILD_SHARED_LIBS=${BUILD_SHARED_LIBS}")

add_definitions(-D_USE_LINUX_API)

if(_DN_USE_VARIANT_API)
  add_definitions(-D_DN_USE_VARIANT_API=1)
else(_DN_USE_VARIANT_API)
  add_definitions(-D_DN_USE_VARIANT_API=0)
endif(_DN_USE_VARIANT_API)

if(_DN_USE_BSTR_API)
  add_definitions(-D_DN_USE_BSTR_API=1)
else(_DN_USE_BSTR_API)
  add_definitions(-D_DN_USE_BSTR_API=0)
endif(_DN_USE_BSTR_API)

if(BUILD_SHARED_LIBS)
  set(SHARED_LIB SHARED)
else(BUILD_SHARED_LIBS)
  set(SHARED_LIB STATIC)
endif(BUILD_SHARED_LIBS)

find_package(ament_cmake REQUIRED)

set(BCAP_LIBRARIES bcap_client pthread)
include_directories(include)

add_library(bcap_client ${SHARED_LIB}
  src/bCAPClient/bcap_client.c
  src/bcap_common.c
  src/dn_com.c
  src/dn_common.c
  src/dn_device.c
  src/dn_socket.c
  src/dn_tcp.c
  src/dn_thread.c
  src/dn_udp.c
)

target_include_directories(bcap_client PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/bCAPClient>
  $<INSTALL_INTERFACE:include>
)

if(_USE_LINUX_API)
  target_link_libraries(bcap_client
    pthread
  )
endif(_USE_LINUX_API)

# bcap_server library
add_library(bcap_server ${SHARED_LIB}
  src/bCAPServer/bcap_server.c
  src/bcap_common.c
  src/dn_com.c
  src/dn_common.c
  src/dn_device.c
  src/dn_socket.c
  src/dn_tcp.c
  src/dn_thread.c
  src/dn_udp.c
)

target_include_directories(bcap_server PUBLIC
  #include/bCAPServer
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/bCAPServer>
  $<INSTALL_INTERFACE:include>
)

if(_USE_LINUX_API)
  target_link_libraries(bcap_server
    pthread
  )
endif(_USE_LINUX_API)

# rac_string library
add_library(rac_string ${SHARED_LIB}
  src/RACString/rac_string.c
  src/dn_common.c
)

target_include_directories(rac_string PUBLIC
	#include/RACString
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/RACString>
  $<INSTALL_INTERFACE:include>
)

# tpcomm library
add_library(tpcomm ${SHARED_LIB}
  src/TPComm/dn_tpcomm.c
  src/dn_com.c
  src/dn_common.c
  src/dn_device.c
  src/dn_robotalk.c
  src/dn_socket.c
  src/dn_thread.c
  src/dn_udp.c
)

target_include_directories(tpcomm PUBLIC
	#include/TPComm
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/TPComm>
  $<INSTALL_INTERFACE:include>
)

if(_USE_LINUX_API)
  target_link_libraries(tpcomm
    pthread
  )
endif(_USE_LINUX_API)

#
#
ament_export_targets(bcap_client bcap_server rac_string tpcomm HAS_LIBRARY_TARGET)
#
#
#
install(TARGETS bcap_client
  EXPORT bcap_client
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  #INCLUDES DESTINATION include
)

install(TARGETS bcap_server
  EXPORT bcap_server
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  #INCLUDES DESTINATION include
)

install(TARGETS rac_string
  EXPORT rac_string
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  #INCLUDES DESTINATION include
)

install(TARGETS tpcomm
  EXPORT tpcomm
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  #INCLUDES DESTINATION include
)

#install(TARGETS bcap_server rac_string tpcomm
#  DESTINATION lib/${PROJECT_NAME}
#  )

install(DIRECTORY include/
    DESTINATION include/${PROJECT_NAME}
    FILES_MATCHING PATTERN "*.h"
    PATTERN "${PROJECT_NAME}" EXCLUDE
    PATTERN ".svn" EXCLUDE
)

ament_export_include_directories(include)
ament_export_libraries(bcap_client bcap_server rac_string tpcomm)

ament_package()

