# Overview
This is a demo protocol for [CommsChampion](https://github.com/arobenko/comms_champion)
ecosystem. It demonstrates protocol that reports its version in the payload first
`Connect` message, and all subsequent messages are treated as having the
reported version.

The protocol is defined in the [schema](dsl/schema.xml)
file using [CommsDSL](https://github.com/arobenko/CommsDSL-Specification).
The **commsds2comms** code generator from [commsdsl](https://github.com/arobenko/commsdsl)
project is used to generate CMake project with proper C++ classes that
implement protocol itself as well as protocol plugin for the
[CommsChampion Tools](https://github.com/arobenko/comms_champion#commschampion-tools).

This project also contains two [example](examples) 
applications (client + server), which demonstrate exchange of the protocol
messages over TCP/IP link.

# Protocol
The contents of the protocol messages demonstrate existence of the fields
based on the protocol version information, reported in the payload of one
of the messages. 

The transport framing is
```
SYNC (2 bytes) | SIZE (2 bytes) | ID (1 byte) | PAYLOAD | CHECKSUM (2 bytes)
```
where
- **SYNC** is synchronization bytes, expected to be `0xab 0xcd`.
- **SIZE** is remaining length (including **CHECKSUM**)
- **ID** is numeric ID of the message.
- **PAYLOAD** is message payload.
- **CHECKSUM** is 16 bit **CRC-CCITTT** checksum of `SIZE | ID | PAYLOAD` bytes.

The generated CMake project of this protocol definition is hosted and can be
viewed at [cc.demo3.generated](https://github.com/arobenko/cc.demo3.generated)

# Examples
The [client](examples/client) reads requested version value from standard input.
Once proper version is entered, it prepares and sends `Connect`  message to 
the server to report protocol version, then immediatelly sends `Msg1` 
message adjusted to have fields relevant to the version. 

The [server](examples/server) records protocol version received in `Msg1` message
and prints values of received 'Msg1` message's fields. No message is sent back
to the client.

# License
Please read [License](https://github.com/arobenko/commsdsl#license)
section from [commsdsl](https://github.com/arobenko/commsdsl) project.

# How to Build
This project uses CMake as its build system. Please open main
[CMakeLists.txt](CMakeLists.txt) file and review available options as well as
mentioned available parameters, which can be used in addition to standard 
ones provided by CMake itself, to modify the default build. 

The **commsdsl2comms** application from [commsdsl](https://github.com/arobenko/commsdsl)
project is used to generate appropriate C++ code. If path to externally
built **commsdsl2comms** is not provided, then this project will build the
latter itself.

The [example](#examples) applications use [Boost](https://www.boost.org)
to parse their command line parameters as well as manage their asynchronous I/O. 
In case Boost libraries are not installed in expected default location
(mostly happens on Windows systems), use variables described in 
[CMake documentation](https://cmake.org/cmake/help/v3.8/module/FindBoost.html) 
to help CMake find required libraries and headers. 
It is recommended to use `-DBoost_USE_STATIC_LIBS=ON` parameter to force
linkage with static Boost libraries.

**NOTE**, that **commsdsl2comms** application also requires 
[Boost](https://www.boost.org) libraries. Please provide path to the latter, when
**commsdsl2comms** is built internally, even if example applications are excluded
from the build.

If code generation output directory is not provided, it defaults to 
`output` subdirectory of the one used to build the project.

### Linux Build
```
$> cd /source/of/this/project
$> mkdir build && cd build
$> cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$PWD/install 
$> make install
```
To build the generated project continue with
```
$> cd output && mkdir build && cd build
$> cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$PWD/install ..
$> make install
```

### Windows Build
```
$> cd C:\source\of\this\project
$> mkdir build
$> cd build
$> cmake -G "NMake Makefiles" -DCMAKE_BUILD_TYPE=Release \ 
    -DCMAKE_INSTALL_PREFIX=%cd%/install \
    -DBOOST_ROOT="C:\Libraries\boost_1_65_1" -DBoost_USE_STATIC_LIBS=ON ..
$> nmake install
```
To build the generated project continue with
```
$> cd output 
$> mkdir build 
$> cd build
$> cmake -G "NMake Makefiles" -DCMAKE_BUILD_TYPE=Release \
    -DCC_QT_DIR=C:\Qt\5.6.3\msvc2015 -DCMAKE_INSTALL_PREFIX=%cd%/install ..
$> nmake install
```
To deploy Qt5 libraries into the installation directory continue with
```
$> nmake deploy_qt
```

# How to Use Generated Code
Please read [How to Use Generated Code](https://github.com/arobenko/commsdsl#how-to-use-generated-code)
guidelines from [commsdsl](https://github.com/arobenko/commsdsl) project.

The [release](https://github.com/arobenko/cc.demo3.commsdsl/releases)
artifacts contain doxygen generated documentation of the protocol definition.

# Contact Information
For bug reports, feature requests, or any other question you may open an issue
here in **github** or e-mail me directly to: **arobenko@gmail.com**. I usually
respond within 24 hours.

