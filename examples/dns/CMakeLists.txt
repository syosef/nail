cmake_minimum_required(VERSION 3.0)

project(dns)

set(CMAKE_C_STANDARD 99)

list(APPEND dns_server_srcs
    dns.c
    server.c
    )

list(APPEND dns_resolver_srcs
    dns.c
    resolver.c
    )

add_cnail(dns_server_srcs
    dns.nail
    )

add_cnail(dns_resolver_srcs
    dns.nail
    )

add_executable(dns_server ${dns_server_srcs})
target_include_directories(dns_server PRIVATE ../../include)

#add_executable(dns_resolver ${dns_resolver_srcs})
#target_include_directories(dns_resolver PRIVATE ../../include)
