# AutobahnC++ cmake build script.
#
# This script uses the following environment variables (if defined):
#
# * BOOST_ROOT
# * MSGPACK_ROOT
# * WEBSOCKETPP_ROOT
#
# When those are defined, they take precedence over any host system
# installations for the respective library. When an explicit library
# root location hasn't been set, cmake will look in other places.

# if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
#     set(CMAKE_CXX_FLAGS "-stdlib=libc++ -Wno-unused-local-typedefs -Wno-unknown-warning-option ${CMAKE_CXX_FLAGS}")
#     if ("${CMAKE_GENERATOR}" STREQUAL "Ninja")
#         set(CMAKE_CXX_FLAGS "-fcolor-diagnostics ${CMAKE_CXX_FLAGS}")
#     endif()
# endif()
#
# if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
#     if(NOT CMAKE_CXX_COMPILER_VERSION VERSION_LESS "4.9.0") # i.e. if >= 4.9.0
#         if (CMAKE_GENERATOR STREQUAL "Ninja")
#             set(CMAKE_CXX_FLAGS "-fdiagnostics-color=always ${CMAKE_CXX_FLAGS}")
#         else()
#             set(CMAKE_CXX_FLAGS "-fdiagnostics-color=auto ${CMAKE_CXX_FLAGS}")
#         endif()
#     endif()
# endif()

