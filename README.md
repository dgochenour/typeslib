# typeslib
Example showing how generated DDS type-support files might be packaged as a library

## Prerequisites 

The `NDDSHOME` environment variable must be set.
## Building the library

    mkdir build
    cd build/
    cmake -DCMAKE_BUILD_TYPE=[Release|Debug] ..
    make install

## Current Limitations

1) This example was tested against Connext Professional 6.1.1 on an x64 Ubuntu 20.04 host
2) The `CMakeLists.txt` file assumes the `C++11` API is being used. The build file could be manually modified to support other APIs.