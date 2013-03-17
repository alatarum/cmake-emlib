About
=====

This project is used to develop applications for efm32 and efr4d - EnergyMicro's ARM Cortex-M MCUs, using cmake, GCC, newlib (libc) and EnergyMicro CMSIS Library. For now it support only EFM32G, EFM32TG, EFM32LG and EFM32GG, but it is easy to add other families in the future.

###Requirements:
* cmake >= 2.8 (not test on 2.6 but may be it works too)
* GCC toolchain with newlib.
* emlib Peripherals Library

###Project contains:
* CMake toolchain file, that configures cmake to use ARM toolchain.
* CMake projects that builds emlib into static libraries.
* CMake modules to find and configure emlib.
* CMake project template.

**Note:** currently tested only on GNU/Linux with CodeSourcery toolchain.

Building & Installing
=====================

First of all you need to configure toolchain and libraries, you can do this by editing gcc_cortex_m.cmake or pass it throught command line.
Variables for toolchain:
* TOOLCHAIN_PREFIX - where toolchain is located, **default**: */usr*
* TARGET_TRIPLET - toolchain target triplet, **default**: *arm-none-eabi*

Additionaly you need to configure emlib:
* EMLIB_DIR - path to EnergyMicro Library (where emlib, CMSIS and Device directories are) **default**: */opt/energymicro*
* TARGET_MCU - EFM32 chip, subfamily or family to build library for (can be specified several values as ';'-separated list) **default**: *"EFM32G;EFM32TG;EFM32LG;EFM32GG"*
* USE_ASSERT - Use internal asserts in emlib (*not implemented yet*)

And some CMake internal variables:
* CMAKE_INSTALL_PREFIX - path where compiled libraries, headers and other files would be installed
* CMAKE_BUILD_TYPE - Build Release or Debug version of libraries

Build emlib
===========

In emlib folder:
```
cmake -DCMAKE_TOOLCHAIN_FILE=../gcc_cortex_m.cmake -DTOOLCHAIN_PREFIX=<path_to_toolchain> -DCMAKE_INSTALL_PREFIX=<path_to_toolchain>/arm-none-eabi/ -DEMLIB_DIR=<path_to_emlib> -DCMAKE_BUILD_TYPE=Release -DTARGET_MCU=efm32g
make install
```
This will build emlib for each parts you are specified. It can take a long time. You can use parallel build to decrease it:
```
make -j<n> install
```
Where <n> is nuber of build jobs at same time.

**Note:** You can use different CMAKE_INSTALL_PREFIX, but than you'll have to configure cmake search paths when using cmake modules.

Usage
=====

After building you need to copy cmake modules in cmake's modules path, or just set CMAKE_MODULE_PATH in project.
Then you need to adjust some variables in CMakeLists.txt:
* PROJECT(efm32-template) - Set the project name.
* FIND_PACKAGE(EmLib REQUIRED) - search for EmLib.
* All projects sources should be listed in PROJECT_SOURCES variable.

Build
=====

###Generate Makefile
```
cmake -DEM_MCU=<chip_name> -DCMAKE_TOOLCHAIN_FILE=<path_to_gcc_cortex_m.cmake> -DCMAKE_BUILD_TYPE=Debug <path_to_source_dir>
```
Where <chip_name> full name of part you are using (for example EFM32G232F128). To build in relise mode use -DCMAKE_BUILD_TYPE=Relise

###Build
```
make
```
The result is a .elf and .bin files by default. You can edit your projects CMakeLists.txt to generate files that you need.
If you want to see full commands used in build process use
```
make VERBOSE=1
```

###For using with Code::Blocks
```
cmake -DEM_MCU=<chip_name> -DCMAKE_TOOLCHAIN_FILE=<path_to_gcc_cortex_m.cmake> -DCMAKE_BUILD_TYPE=Debug -G "CodeBlocks - Unix Makefiles" <path_to_source_dir>
```

###For using with Eclipse CDT
```
cmake -DEM_MCU=<chip_name> -DCMAKE_TOOLCHAIN_FILE=<path_to_gcc_cortex_m.cmake> -DCMAKE_BUILD_TYPE=Debug -G "Eclipse CDT4 - Unix Makefiles" <path_to_source_dir>
```

TODO
====

* Enable asserts
* Allow to change linker settings (stack/heap size, move sections, etc)
* Move startup file to library
* Add 'd' suffix for debug builds
* Enable LTO

Thanks
======

Original scripts author is Konstantin Oblaukhov (https://github.com/ObKo/stm32-cmake)
