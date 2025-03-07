cmake_minimum_required(VERSION 3.0)
project(ptpd2)

add_compile_options(
    -DDATADIR=\"/data/local/tmp\"
    -DHAVE_CONFIG_H
    -DPTPD_EXPERIMENTAL
    -DPTPD_STATISTICS
    -DPTPD_PTIMERS
    -DPTPD_UNICAST_MAX=128
    -Wall
    -fexceptions
    -g
    -O2
    -fstack-protector
)

set(SOURCE_FILES
    src/arith.c
    src/bmc.c
    src/ptp_timers.c
    src/display.c
    src/management.c
    src/signaling.c
    src/protocol.c
    src/timingdomain.c
    src/ptpd.c
    src/dep/ipv4_acl.c
    src/dep/msg.c
    src/dep/net.c
    src/dep/eventtimer.c
    src/dep/servo.c
    src/dep/configdefaults.c
    src/dep/daemonconfig.c
    src/dep/startup.c
    src/dep/sys.c
    src/dep/alarms.c
    src/dep/statistics.c
    src/dep/outlierfilter.c
    src/dep/eventtimer_posix.c
    src/dep/iniparser/dictionary.c
    src/dep/iniparser/iniparser.c
    src/dep/ntpengine/ntp_isc_md5.c
    src/dep/ntpengine/ntpdcontrol.c
)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)


add_executable(${PROJECT_NAME} ${SOURCE_FILES})

target_include_directories(${PROJECT_NAME} PRIVATE 
    ${CMAKE_CURRENT_SOURCE_DIR}/src 
    ${CMAKE_CURRENT_SOURCE_DIR}
    # ${PCAP_INCLUDE_DIR}
    )

target_link_libraries(${PROJECT_NAME}
    m
    rt
)

find_package(PCAP)

message("-- PCAP_FOUND          :${PCAP_FOUND}")
message("-- PCAP_INCLUDE_DIR    :${PCAP_INCLUDE_DIR}")
message("-- PCAP_LIBRARY        :${PCAP_LIBRARY}")


if (PCAP_FOUND)
    target_compile_definitions(${PROJECT_NAME} PRIVATE PTPD_PCAP HAVE_PCAP_PCAP_H)
    target_include_directories(${PROJECT_NAME} PRIVATE ${PCAP_INCLUDE_DIR})
    target_link_libraries(${PROJECT_NAME} ${PCAP_LIBRARY})
endif()
