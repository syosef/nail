cmake_minimum_required(VERSION 3.0)

project(dns)

#set(CMAKE_C_STANDARD 99)
set(VERBOSE ON)
set(CMAKE_VERBOSE_MAKEFILE ON)
list(APPEND dns_server_srcs
#    dns.cc
    server.cc
    )

list(APPEND dns_resolver_srcs
#    dns.cc
    resolver.cc
    )

add_nail(dns_server_srcs
    dns.nail
    )

add_nail(dns_resolver_srcs
    dns.nail
    )

add_executable(dns_server ${dns_server_srcs})
target_include_directories(dns_server PRIVATE ../../include)

#add_executable(dns_resolver ${dns_resolver_srcs})
#target_include_directories(dns_resolver PRIVATE ../../include)
