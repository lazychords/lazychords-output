CMAKE_MINIMUM_REQUIRED(VERSION 2.8.7)
ENABLE_TESTING()


set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR})


INCLUDE(tools/FindFMOD.cmake)

IF(NOT FMOD_FOUND)
        IF("${CMAKE_SYSTEM}" MATCHES "Linux")
            MESSAGE(WARNING "FMODex was not found, trying to download a local version of it...")
            include(ExternalProject)
            ExternalProject_Add(
                FMODex
                PREFIX ${PROJECT_SOURCE_DIR}/ext
                TMP_DIR ${PROJECT_SOURCE_DIR}/ext
                DOWNLOAD_DIR ${PROJECT_SOURCE_DIR}/ext
                URL http://www.fmod.org/download/fmodex/api/Linux/fmodapi44449linux.tar.gz
                UPDATE_COMMAND mkdir ${PROJECT_SOURCE_DIR}/libs/fmodex
                PATCH_COMMAND rm -rf documentation examples fmoddesignerapi Makefile tools
                CONFIGURE_COMMAND cp ../FMODex/api/inc/fmod.h ../FMODex/api/inc/fmod_codec.h ../FMODex/api/inc/fmod_dsp.h  ../FMODex/api/inc/fmod_errors.h  ../FMODex/api/inc/fmod.hpp ../FMODex/api/inc/fmodlinux.h  ../FMODex/api/inc/fmod_memoryinfo.h ../FMODex/api/inc/fmod_output.h ../FMODex/api/lib/libfmodex-4.44.49.so ../FMODex/api/lib/libfmodex64.so  ../FMODex/api/lib/libfmodexL64-4.44.49.so  ../FMODex/api/lib/libfmodexL.so ../FMODex/api/lib/libfmodex64-4.44.49.so  ../FMODex/api/lib/libfmodexL-4.44.49.so  ../FMODex/api/lib/libfmodexL64.so ../FMODex/api/lib/libfmodex.so ${PROJECT_SOURCE_DIR}/libs/fmodex/
               BUILD_COMMAND ""
               INSTALL_COMMAND rm -rf ../FMODex/api
               TEST_COMMAND ""
               LOG_DOWNLOAD 1         # Wrap download in script to log output
               LOG_UPDATE 1             # Wrap update in script to log output
               LOG_CONFIGURE 1           # Wrap configure in script to log output
               LOG_BUILD 1               # Wrap build in script to log output
               LOG_TEST 1                # Wrap test in script to log output
               LOG_INSTALL 1
           )
           INCLUDE(tools/TargetArch.cmake)
           target_architecture(ARCH)
           IF((ARCH STREQUAL "x86_64"))
               SET(FMOD_LIBRARY ${PROJECT_SOURCE_DIR}/libs/fmodex/libfmodex64.so)
           ELSE()
               SET(FMOD_LIBRARY ${PROJECT_SOURCE_DIR}/libs/fmodex/libfmodex.so)
           ENDIF()
       ELSE()
            MESSAGE(FATAL_ERROR "FMODex is required to build the project. Please install it by hand and make sure it is in an accessible location")
       ENDIF()
        
ELSE()
    INCLUDE_DIRECTORIES(${FMOD_INCLUDE_DIR})
ENDIF()

FIND_PACKAGE(Doxygen)

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -O3 -Wall -pedantic -Wextra")
SET(SOFT_FLAGS "${CMAKE_CXX_FLAGS}")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wcast-qual -Wcast-align -Wfloat-equal -Wpointer-arith")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wunreachable-code -Wchar-subscripts -Wcomment -Wformat")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Werror-implicit-function-declaration -Wmain -Wmissing-braces")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wparentheses -Wsequence-point -Wreturn-type -Wswitch")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wuninitialized -Wreorder -Wundef -Wshadow -Wwrite-strings")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wsign-compare  -Wconversion")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wmissing-noreturn  -Wsign-conversion -Wunused ")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wstrict-aliasing -Wstrict-overflow -Wconversion")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wdisabled-optimization -Wsuggest-attribute=noreturn ")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}  -Wsuggest-attribute=pure ")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wlogical-op -Wunsafe-loop-optimizations ")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}  -Wunused-parameter -Wshadow  -Weffc++")
ADD_DEFINITIONS(-DNOT_USE_ML)

############################
# ABCToMidi
############################

ADD_SUBDIRECTORY(tools/ABCToMidi)
INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR}/tools/)


############################
# LIBS
############################

INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR}/libs/)

include(ExternalProject)
ExternalProject_Add(liblazychord
  DOWNLOAD_COMMAND echo "download"
  PREFIX ${PROJECT_SOURCE_DIR}/libs/liblazychord
  SOURCE_DIR ${PROJECT_SOURCE_DIR}/libs/liblazychord
  BINARY_DIR ${PROJECT_SOURCE_DIR}/libs/liblazychord
  INSTALL_COMMAND echo "success"
#  CONFIGURE_COMMAND mkdir -p build && cd build && cmake ..
#  BUILD_STEP make
#  BUILD_STEP cmake .. && make -j
)
INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR}/libs/liblazychord/src/)
############################
# Output Lib
############################

SET(OutputLib_SRCS
    src/Track.cpp
)



INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR}/src/)
ADD_LIBRARY(OutputLib SHARED ${OutputLib_SRCS})
ADD_DEPENDENCIES(OutputLib liblazychord)
TARGET_LINK_LIBRARIES(OutputLib ABCToMidi)
TARGET_LINK_LIBRARIES(OutputLib ${FMOD_LIBRARY})


############################
# Tests
############################

#SET(CMAKE_CXX_FLAGS "${SOFT_FLAGS}")
#SET(Tests_SRCS
#    tests/MusicRulesTest.cpp
#    tests/musicTest.cpp
#    tests/TestMain.cpp
#)

#INCLUDE_DIRECTORIES(tests/)
#ADD_EXECUTABLE(runTests ${Tests_SRCS})
#TARGET_LINK_LIBRARIES(runTests LazyChordsLib)
#TARGET_LINK_LIBRARIES(runTests m)

#ADD_TEST(runTests runTests -d yes)

############################
# DOxygen
############################
IF(DOXYGEN_FOUND)
    CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile @ONLY)
    ADD_CUSTOM_TARGET(docOutput
        ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile
        WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
        COMMENT "Generating API documentation with Doxygen" VERBATIM
    )
ENDIF(DOXYGEN_FOUND)