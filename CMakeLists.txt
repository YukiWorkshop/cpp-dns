cmake_minimum_required(VERSION 3.2)
project(cpp-dns)

set(CMAKE_CXX_STANDARD 17)

add_library(cpp-dns DNSResolver.cpp DNSResolver.hpp)
add_executable(cpp_dns_test Test.cpp)

target_link_libraries(cpp-dns boost_system pthread udns)
target_link_libraries(cpp_dns_test cpp-dns)