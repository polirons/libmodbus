CMAKE_MINIMUM_REQUIRED(VERSION 2.6.0)

PROJECT(LIBMODBUS)

SET(LIBRARY_OUTPUT_PATH ${CMAKE_CURRENT_BINARY_DIR})
SET(EXECUTABLE_OUTPUT_PATH ${CMAKE_CURRENT_BINARY_DIR})

INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR})
INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR})

SET(LIBMODBUS_VERSION_MAJOR 3)
SET(LIBMODBUS_VERSION_MINOR 1)
SET(LIBMODBUS_VERSION_MICRO 10)
SET(LIBMODBUS_VERSION ${LIBMODBUS_VERSION_MAJOR}.${LIBMODBUS_VERSION_MINOR}.${LIBMODBUS_VERSION_MICRO})

SET(HAVE_DECL_TIOCM_RTS 1)

configure_file(src/modbus-version.h.in src/modbus-version.h)
configure_file(src/config.h.in src/config.h)


SET(HEADER_FILES
    src/config.h.in
    src/modbus.h
    src/modbus-private.h 
    src/modbus-rtu.h
    src/modbus-rtu-private.h
    src/modbus-tcp.h
    src/modbus-tcp-private.h
    src/modbus-version.h.in)

SET(SOURCE_FILES
    src/modbus.c
    src/modbus-tcp.c
    src/modbus-rtu.c
    src/modbus-data.c)

ADD_LIBRARY(modbus ${HEADER_FILES} ${SOURCE_FILES})

target_include_directories(modbus PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/src)

#------------------------------------------------------------------------------

INSTALL(FILES src/modbus.h DESTINATION include/modbus)
INSTALL(FILES src/modbus-rtu.h DESTINATION include/modbus)
INSTALL(FILES src/modbus-tcp.h DESTINATION include/modbus)
INSTALL(FILES src/modbus-version.h DESTINATION include/modbus)

INSTALL(TARGETS modbus DESTINATION lib)

