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