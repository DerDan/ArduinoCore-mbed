cmake_minimum_required(VERSION 3.20)


set(CMAKE_CXX_STANDARD 14)

set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_PROCESSOR CORTEX)
set(CROSS_COMPILE "arm-")

set(CMAKE_C_COMPILER_FORCED true)
set(CMAKE_CXX_COMPILER_FORCED true)


project(ArduinoCore_mbed)


file(STRINGS variants/PORTENTA_H7_M7/cflags.txt CFLAGS_TXT REGEX "-[^D].*")
file(STRINGS variants/PORTENTA_H7_M7/cxxflags.txt CXXFLAGS_TXT REGEX "-[^D].*")

file(STRINGS variants/PORTENTA_H7_M7/cflags.txt CDEFINES_TXT REGEX "-D.*")
file(STRINGS variants/PORTENTA_H7_M7/cxxflags.txt CXXDEFINES_TXT REGEX "-D.*")

file(STRINGS variants/PORTENTA_H7_M7/defines.txt DEFINES_TXT)
file(STRINGS variants/PORTENTA_H7_M7/includes.txt INCLUDES_TXT)


list(TRANSFORM CDEFINES_TXT REPLACE "-D" "")
list(TRANSFORM CXXDEFINES_TXT REPLACE "-D" "")
list(TRANSFORM DEFINES_TXT REPLACE "-D" "")
list(TRANSFORM INCLUDES_TXT REPLACE "-iwithprefixbefore/mbed" "${CMAKE_CURRENT_LIST_DIR}/cores/arduino/mbed")

#file(GLOB_RECURSE SRC_FILES CONFIGURE_DEPENDS cores/*.cpp cores/*.c)
file(GLOB_RECURSE SRC_FILES CONFIGURE_DEPENDS cores/*.cpp cores/*.c variants/PORTENTA_H7_M7/*.cpp variants/PORTENTA_H7_M7/*.c)
file(GLOB_RECURSE H_FILES CONFIGURE_DEPENDS cores/*.h variants/PORTENTA_H7_M7/*.h)

#file(GLOB_RECURSE SRC_VFILES CONFIGURE_DEPENDS variants/PORTENTA_H7_M7/*.cpp variants/PORTENTA_H7_M7/*.c)


foreach (S ${SRC_FILES})
    message("SRC_FILES: ${S}")
endforeach ()
#foreach (f ${CFLAGS_TXT})
#    message("C:  ${f}")
#endforeach ()
#
#foreach (f ${CXXFLAGS_TXT})
#    message("CXX: ${f}")
#endforeach ()

foreach (i ${INCLUDES_TXT})
    IF (NOT EXISTS ${i})
        message("not inc: ${i}")
    endif ()
endforeach ()


add_compile_options(
        "$<$<COMPILE_LANGUAGE:C>:${CFLAGS_TXT}>"
        "$<$<COMPILE_LANGUAGE:CXX>:${CXXFLAGS_TXT}>"
)
add_compile_definitions(
        "$<$<COMPILE_LANGUAGE:C>:${CDEFINES_TXT}>"
        "$<$<COMPILE_LANGUAGE:CXX>:${CXXDEFINES_TXT}>"
#        ARDUINO_AS_MBED_LIBRARY
#        USE_ARDUINO_PINOUT
        ${DEFINES_TXT}
)


include_directories(
        ${INCLUDES_TXT}
)

add_compile_definitions(
        ARDUINO=10816
        ARDUINO_PORTENTA_H7_M7
        #        ARDUINO_ARCH_ARDUINOCORE-MBED
        ARDUINO_ARCH_MBED
        ARDUINO_LIBRARY_DISCOVERY_PHASE=0
        CM4_BINARY_START=0x08100000
)

include_directories(
        cores/arduino
        variants/PORTENTA_H7_M7
        cores/arduino/api/deprecated
        cores/arduino/api/deprecated-avr-comp
)
#
add_library(ArduinoCore_mbed
        ${SRC_FILES}
        ${H_FILES}
        )
