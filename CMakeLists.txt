cmake_minimum_required(VERSION 3.10)
project(libmodbus VERSION 3.1.10 LANGUAGES C)

set(CMAKE_C_STANDARD 99)

# Sürüm bilgileri
set(LIBMODBUS_VERSION_MAJOR 3)
set(LIBMODBUS_VERSION_MINOR 1)
set(LIBMODBUS_VERSION_MICRO 10)
set(LIBMODBUS_VERSION ${LIBMODBUS_VERSION_MAJOR}.${LIBMODBUS_VERSION_MINOR}.${LIBMODBUS_VERSION_MICRO})

# ABI version
set(LIBMODBUS_LD_CURRENT 6)
set(LIBMODBUS_LD_REVISION 0)
set(LIBMODBUS_LD_AGE 1)
set(LIBMODBUS_LT_VERSION_INFO ${LIBMODBUS_LD_CURRENT}:${LIBMODBUS_LD_REVISION}:${LIBMODBUS_LD_AGE})

# Derleme bayrakları ve seçenekleri
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wmissing-declarations -Wmissing-prototypes -Wnested-externs -Wpointer-arith -Wpointer-arith -Wsign-compare -Wchar-subscripts -Wstrict-prototypes -Wshadow -Wformat-security")

# Başlık dosyaları ve fonksiyonlarının kontrolü
include(CheckIncludeFiles)
include(CheckFunctionExists)
include(CheckSymbolExists)

check_include_files(arpa/inet.h HAVE_ARPA_INET_H)
check_include_files(byteswap.h HAVE_BYTESWAP_H)
check_include_files(errno.h HAVE_ERRNO_H)
check_include_files(fcntl.h HAVE_FCNTL_H)
check_include_files(limits.h HAVE_LIMITS_H)
check_include_files(linux/serial.h HAVE_LINUX_SERIAL_H)
check_include_files(netdb.h HAVE_NETDB_H)
check_include_files(netinet/in.h HAVE_NETINET_IN_H)
check_include_files(netinet/tcp.h HAVE_NETINET_TCP_H)
check_include_files(sys/ioctl.h HAVE_SYS_IOCTL_H)
check_include_files(sys/params.h HAVE_SYS_PARAMS_H)
check_include_files(sys/socket.h HAVE_SYS_SOCKET_H)
check_include_files(sys/time.h HAVE_SYS_TIME_H)
check_include_files(sys/types.h HAVE_SYS_TYPES_H)
check_include_files(termios.h HAVE_TERMIOS_H)
check_include_files(time.h HAVE_TIME_H)
check_include_files(unistd.h HAVE_UNISTD_H)

check_function_exists(accept4 HAVE_ACCEPT4)
check_function_exists(getaddrinfo HAVE_GETADDRINFO)
check_function_exists(gettimeofday HAVE_GETTIMEOFDAY)
check_function_exists(inet_pton HAVE_INET_PTON)
check_function_exists(inet_ntop HAVE_INET_NTOP)
check_function_exists(select HAVE_SELECT)
check_function_exists(socket HAVE_SOCKET)
check_function_exists(strerror HAVE_STRERROR)
check_function_exists(strlcpy HAVE_STRLCPY)

check_symbol_exists(TIOCSRS485 "sys/ioctl.h" HAVE_TIOCSRS485)
check_symbol_exists(TIOCM_RTS "sys/ioctl.h" HAVE_TIOCM_RTS)

check_symbol_exists(__CYGWIN__ "stddef.h" HAVE_DECL___CYGWIN__)

if(CMAKE_SYSTEM_NAME MATCHES "Linux"
  OR CMAKE_SYSTEM_NAME MATCHES "GNU/kFreeBSD"
  OR CMAKE_SYSTEM_NAME MATCHES "GNU/Hurd"
  OR CYGWIN)
  add_definitions(-D_GNU_SOURCE)
endif()

SET(HEADER_FILES
    src/modbus.h
    src/modbus-private.h
    src/modbus-rtu.h
    src/modbus-rtu-private.h
    src/modbus-tcp.h
    src/modbus-tcp-private.h)

SET(SOURCE_FILES
    src/modbus.c
    src/modbus-tcp.c
    src/modbus-rtu.c
    src/modbus-data.c)

add_library(libmodbus SHARED
    ${HEADER_FILES}
    ${SOURCE_FILES}
)
set_target_properties(libmodbus PROPERTIES
    VERSION ${LIBMODBUS_VERSION}
    SOVERSION ${LIBMODBUS_LD_CURRENT}
    OUTPUT_NAME "modbus"
    PREFIX "lib"
)

target_include_directories(libmodbus
    PUBLIC src
    PRIVATE ${CMAKE_CURRENT_BINARY_DIR}
)

add_library(libmodbus-static STATIC
    $<TARGET_OBJECTS:libmodbus>
)

set_target_properties(libmodbus-static PROPERTIES
    VERSION ${LIBMODBUS_VERSION}
    OUTPUT_NAME "modbus"
    PREFIX "lib"
)

target_include_directories(libmodbus-static
    PUBLIC src
    PRIVATE ${CMAKE_CURRENT_BINARY_DIR}
)

# pkg-config dosyası
configure_file(libmodbus.pc.in libmodbus.pc @ONLY)

# Yapılandırma dosyaları
configure_file(src/modbus-version.h.in modbus-version.h @ONLY)
configure_file(src/config.h.in config.h @ONLY)

# Install targets
include(GNUInstallDirs)
install(TARGETS libmodbus libmodbus-static
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
install(FILES src/modbus.h src/modbus-rtu.h src/modbus-tcp.h ${CMAKE_CURRENT_BINARY_DIR}/modbus-version.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
install(FILES ${CMAKE_BINARY_DIR}/libmodbus.pc
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig
)

