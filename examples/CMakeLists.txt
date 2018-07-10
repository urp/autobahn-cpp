
find_package( Boost REQUIRED
  COMPONENTS
    program_options
)

add_library(examples_parameters parameters.cpp )
target_link_libraries( examples_parameters PRIVATE Boost::program_options )

add_executable(caller caller.cpp )
target_link_libraries(caller examples_parameters autobahn-cpp::autobahn-cpp)

add_executable(callee callee.cpp )
target_link_libraries(callee examples_parameters autobahn-cpp::autobahn-cpp)

add_executable(provide_prefix callee.cpp )
target_link_libraries(provide_prefix examples_parameters autobahn-cpp::autobahn-cpp)

add_executable(publisher publisher.cpp )
target_link_libraries(publisher examples_parameters autobahn-cpp::autobahn-cpp)

add_executable(subscriber subscriber.cpp )
target_link_libraries(subscriber examples_parameters autobahn-cpp::autobahn-cpp)

add_executable(wampcra wampcra.cpp )
target_link_libraries(wampcra examples_parameters OpenSSL::SSL autobahn-cpp::autobahn-cpp)

add_executable(uds uds.cpp )
target_link_libraries(uds examples_parameters autobahn-cpp::autobahn-cpp)

add_executable(websocket_callee websocket_callee.cpp )
target_link_libraries(websocket_callee examples_parameters autobahn-cpp::autobahn-cpp)

