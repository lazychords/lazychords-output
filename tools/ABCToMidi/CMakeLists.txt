SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")

SET(ABCToMidi_SRCS
    genmidi.c
    midifile.c
    parseabc.c
    parser2.c
    queues.c
    store.c
)

INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR}/tools/)
ADD_LIBRARY(ABCToMidi ${ABCToMidi_SRCS})