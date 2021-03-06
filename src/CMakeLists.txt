set(SOURCE_FILES_SNI
        shadowsocks.h
        socks5.h
        socks5.c
        http.c
        tls.c
        rule.c)

set(SOURCE_FILES_OBFS
        ssr_cipher_names.c
        obfs/auth.c
        obfs/auth_chain.c
        obfs/base64.c
        obfs/crc32.c
        obfs/http_simple.c
        obfs/obfs.c
        obfs/obfsutil.c
        obfs/tls1.2_ticket.c
        obfs/verify.c)

set(SOURCE_FILES_LOCAL
        ssrbuffer.c
        ssrbuffer.h
        ssrutils.c
        jconf.c
        json.c
        encrypt.c
        cache.c
        acl.c
        netutils.c
        udprelay.c
        local.c
        common.h
        includeobfs.h
        ${SOURCE_FILES_OBFS}
        ${SOURCE_FILES_SNI})

set(SOURCE_FILES_CLIENT
        cache.c
        cache.h
        encrypt.c
        encrypt.h
        ssrbuffer.c
        ssrbuffer.h
        ssrutils.c
        ssrutils.h
        netutils.c
        netutils.h
        udprelay.c
        udprelay.h
        client/defs.h
        client/listener.c
        client/main.c
        client/s5.c
        client/s5.h
        client/ssrcipher.c
        client/ssrcipher.h
        client/tunnel.c
        client/util.c
        client/util.h
        ${SOURCE_FILES_OBFS}
        )

set(SOURCE_FILES_TUNNEL
        utils.c
        jconf.c
        json.c
        encrypt.c
        udprelay.c
        cache.c
        netutils.c
        tunnel.c)

set(SOURCE_FILES_SERVER
        utils.c
        jconf.c
        json.c
        encrypt.c
        udprelay.c
        cache.c
        acl.c
        resolv.c
        netutils.c
        server.c
        ${SOURCE_FILES_SNI})

set(SOURCE_FILES_MANAGER
        utils.c
        jconf.c
        json.c
        manager.c)

set(SOURCE_FILES_REDIR
        ssrutils.c
        jconf.c
        json.c
        encrypt.c
        netutils.c
        cache.c
        udprelay.c
        redir.c
        ${SOURCE_FILES_SNI})

include_directories(obfs)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${LIBUVDIR}/include)
include_directories(${libcork_include_dirs})
include_directories(${libipset_include_dirs})
include_directories(${libudns_include_dirs})
include_directories(${libsodium_include_dirs})
include_directories(${LIB_JSON_C_DIR}/..)
include_directories(${PCRE_INCLUDE_DIR})

if (WIN32)
    list ( APPEND SOURCE_FILES_LOCAL win32.c )
    list ( APPEND SOURCE_FILES_TUNNEL win32.c )
    list ( APPEND SOURCE_FILES_SERVER win32.c )
endif ()

if (!APPLE)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -pthread")
endif()

add_executable(ssr-client ${SOURCE_FILES_CLIENT})
add_executable(ssr-local ${SOURCE_FILES_LOCAL})
#add_executable(ss_tunnel ${SOURCE_FILES_TUNNEL})
#add_executable(ss_server ${SOURCE_FILES_SERVER})
#add_executable(ss_manager ${SOURCE_FILES_MANAGER})
#add_executable(ss_redir ${SOURCE_FILES_REDIR})
#add_library(libssr-native ${SOURCE_FILES_LOCAL})

set_target_properties(ssr-client PROPERTIES COMPILE_DEFINITIONS MODULE_LOCAL)
set_target_properties(ssr-local PROPERTIES COMPILE_DEFINITIONS MODULE_LOCAL)
#set_target_properties(ss_tunnel PROPERTIES COMPILE_DEFINITIONS MODULE_TUNNEL)
#set_target_properties(ss_server PROPERTIES COMPILE_DEFINITIONS MODULE_REMOTE)
#set_target_properties(ss_manager PROPERTIES COMPILE_DEFINITIONS MODULE_MANAGER)
#set_target_properties(ss_redir PROPERTIES COMPILE_DEFINITIONS MODULE_REDIR)

#target_compile_definitions(libssr-native PRIVATE MODULE_LOCAL LIB_ONLY)
#set_target_properties(libssr-native PROPERTIES SUFFIX ".la")

set (ss_lib_common
        json-c
        uv
        m
        libcork)

set (ss_lib_net
        ${ss_lib_common}
        libcrypto.a # ${LIBCRYPTO} # OpenSSL library
        libipset
        pcre #${PCRE_LIBRARIES}
        udns
        sodium
        )

if (WIN32)
    list ( APPEND ss_lib_net Ws2_32 )
endif()


target_link_libraries(ssr-client ${ss_lib_net})

target_link_libraries(ssr-local ${ss_lib_net})

#target_link_libraries(ss_tunnel ${ss_lib_net} )
#target_link_libraries(ss_server ${ss_lib_net} )
#target_link_libraries(ss_manager ${ss_lib_common} )
#target_link_libraries(ss_redir ${ss_lib_net})
