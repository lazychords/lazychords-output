# Output library for the LazyChords project
[![Build Status](https://travis-ci.org/lazychords/lazychords-output.svg?branch=master)](https://travis-ci.org/lazychords/lazychords-output)

This repository hosts the output facilities for the LazyChords project. Currently we use two external libraries :
* ABC2MIDI (http://abc.sourceforge.net/abcMIDI/), used to convert the melodies from ABC format to Midi format
* FMODex (http://www.fmod.org/download-previous-products/), to play the Midi file

# Build instruction

To build the project, you need a recent C++ compiler (this project uses C++11, supported since gcc4.8), cmake, make, the boost library, and the FMODex library. On a linux system, the cmake script will try to download the FMODex library automatically, so if you are lucky, there's nothing to do about it.

```
sudo apt-get install cmake make g++ libboost-dev libboost-test-dev libboost-program-options-dev
git submodule update --init --recursive
mkdir build && cd build
cmake ..
make -j
```
