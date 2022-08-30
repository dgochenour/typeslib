# typeslib

This repository provides a simple example showing how generated DDS type-support files might be packaged as a library. There are, or course, many approaches to every problem and this example demonstrates one.

## Prerequisites 

The `NDDSHOME` environment variable must be set.

## Building the library

    mkdir build
    cd build/
    cmake -DCMAKE_BUILD_TYPE=[Release|Debug] ..
    make install

After completing the steps above, the generated header files will be installed in the `typeslib/out/include` directory, and the static library (or archive) can be found in the `typeslib/out/Debug` or `typeslib/out/Release` directory, depending on the `CMAKE_BUILD_TYPE`.

## Current Limitations

1) This example was tested against Connext Professional 6.1.1 on an x64 Ubuntu 20.04 host
2) The `CMakeLists.txt` file assumes the `C++11` API is being used. The build file could be manually modified to support other APIs.