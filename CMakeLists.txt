cmake_minimum_required(VERSION 3.1)

include( GNUInstallDirs )
include( CMakePackageConfigHelpers )

project( autobahn-cpp VERSION 18.4.1 )

option(AUTOBAHN_BUILD_EXAMPLES "Build examples" ON)

set(CMAKE_CXX_STANDARD 11)

set(CMAKE_CXX_FLAGS "-std=c++11 -Wall -Werror -Wno-unused-variable ${CMAKE_CXX_FLAGS}")

list( APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules/" )

if( BUILD_SHARED_LIBS )
    set( Boost_USE_STATIC_LIBS OFF )
else()
    # https://stackoverflow.com/a/3176086/884770
    set( Boost_USE_STATIC_LIBS ON )
endif()

# Find OpenSSL 1.0
find_package(OpenSSL 1.0 EXACT )
if( NOT OPENSSL_FOUND )
  message( WARNING "OpenSSL 1.0 not found. Using workaround..." )
  if( NOT ( EXISTS /usr/lib/libssl.so.1.0.0 AND IS_DIRECTORY /usr/include/openssl-1.0 ) )
    message( FATAL_ERROR "Unable to find OpenSSL 1.0" )
  endif()
  add_library( OpenSSL::SSL SHARED IMPORTED )
  set_target_properties( OpenSSL::SSL
    PROPERTIES
      IMPORTED_LOCATION /usr/lib/libssl.so.1.0.0
  )
  target_link_libraries( OpenSSL::SSL INTERFACE /usr/lib/libcrypto.so.1.0.0 )
  target_include_directories( OpenSSL::SSL INTERFACE /usr/include/openssl-1.0 )
endif()

# Find other deps

find_package( Boost REQUIRED
  COMPONENTS
    system
    thread
    random
)

find_package( Msgpack REQUIRED )
find_package( Websocketpp REQUIRED )

MESSAGE( STATUS "AUTOBAHN_BUILD_EXAMPLES:  " ${AUTOBAHN_BUILD_EXAMPLES} )
MESSAGE( STATUS "CMAKE_ROOT:               " ${CMAKE_ROOT} )
MESSAGE( STATUS "CMAKE_INSTALL_PREFIX:     " ${CMAKE_INSTALL_PREFIX} )
MESSAGE( STATUS "Boost_INCLUDE_DIRS:       " ${Boost_INCLUDE_DIRS} )
MESSAGE( STATUS "Boost_LIBRARIES:          " ${Boost_LIBRARIES} )
MESSAGE( STATUS "Msgpack_INCLUDE_DIRS:     " ${Msgpack_INCLUDE_DIRS} )
MESSAGE( STATUS "Msgpack_LIBRARIES:        " ${Msgpack_LIBRARIES} )
MESSAGE( STATUS "Websocketpp_INCLUDE_DIRS: " ${Websocketpp_INCLUDE_DIRS} )
MESSAGE( STATUS "Websocketpp_LIBRARIES:    " ${Websocketpp_LIBRARIES} )

add_library( autobahn-cpp INTERFACE )
add_library( autobahn-cpp::autobahn-cpp ALIAS autobahn-cpp )

target_include_directories( autobahn-cpp INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/include>
  ${OPENSSL_INCLUDE_DIR}
  ${Websocketpp_INCLUDE_DIRS}
  ${Msgpack_INCLUDE_DIRS})

target_link_libraries( autobahn-cpp INTERFACE
  Boost::system
  Boost::thread
  Boost::random
  OpenSSL::SSL
)

install( TARGETS autobahn-cpp EXPORT autobahn-cpp-targets )

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/autobahn
  DESTINATION
    ${CMAKE_INSTALL_INCLUDEDIR}
  COMPONENT
    "dev"
)

# Create package build configuration in build directory

export( EXPORT autobahn-cpp-targets
  FILE
    "${CMAKE_CURRENT_BINARY_DIR}/autobahn-cpp-targets.cmake"
  NAMESPACE
    autobahn-cpp::
)

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/autobahn-cpp-config-version.cmake"
  VERSION
    ${${PROJECT_NAME}_VERSION}
  COMPATIBILITY
    AnyNewerVersion
)

configure_package_config_file(
  "${CMAKE_CURRENT_SOURCE_DIR}/autobahn-cpp-config.cmake.in"
  "autobahn-cpp-config.cmake"
  INSTALL_DESTINATION
    "${CMAKE_CURRENT_BINARY_DIR}"
)

# Export build configuration into local cmake repository

export( PACKAGE autobahn-cpp )

# Install package configuration for installed targets

set( package_config_install_dir "${CMAKE_INSTALL_LIBDIR}/cmake/autobahn-cpp" )

install( EXPORT autobahn-cpp-targets
  DESTINATION
    ${package_config_install_dir}
  NAMESPACE
    autobahn-cpp::
  COMPONENT
    dev
)

install(
  FILES
    "${CMAKE_CURRENT_BINARY_DIR}/autobahn-cpp-config.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/autobahn-cpp-config-version.cmake"
  DESTINATION
    ${package_config_install_dir}
  COMPONENT
    dev
)


if(AUTOBAHN_BUILD_EXAMPLES)
    add_subdirectory(examples)
endif(AUTOBAHN_BUILD_EXAMPLES)
