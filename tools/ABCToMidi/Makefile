CC=gcc
RM=rm -f
ifeq ($(WINDOWS), 1)
RM=del /q
endif
CFLAGS=-w -O3

LDFLAGS=  -lm
DEFOBJS = genmidi.o midifile.o parseabc.o parser2.o queues.o store.o 



## Define our target ##
all: $(DEFOBJS)


$(EXE) : $(DEFOBJS)
		 $(CC) $(LDFLAGS) $(DEFOBJS) -o $(EXE)

## Define our compilation steps / dependencies ##
%.o: %.c
	$(CC) -o $@ -c $< $(CFLAGS) $(INCLUDES)



clean:
	$(RM) $(DEFOBJS)


