# ATF build using CMake

* Proposal: [SDL-NNNN](nnnn-atf_build_using_cmake.md)
* Author: [Alexander Kutsan](https://github.com/LuxoftAKutsan)
* Status: **Awaiting review**
* Impacted Platforms: [ATF]

## Introduction

This proposal offers using CMake build system for compilation Automated Test Framework (ATF) project instead of qmake tool. 

## Motivation

Extending of ATF with new dependencies and new code requires seamless integration with build system.  
Existing qmake and make systems are not expandable and adding any supplementary components is rather complicated.  
CMake is able to provide clear dependency structure, seamless integration with 3rd party libraries, and easy expandable project structure.

## Proposed solution

This proposal is about to create CMakeLists.txt files with all required dependency descriptions.

#### Pre build cmake responsibilities:
 - Check if Qt framework is available on the target workstation.
 - Check if lua library is available on the target workstation.
 - Fetch [bson_c_lib](https://github.com/smartdevicelink/bson_c_lib) with lua support and append it to sdl_atf compilation.

#### Build cmake responsibilities:

  - Provide `all` target which will create ATF binary.

  - Provide `install` target that will install required files for running ATF to `CMAKE_INSTALL_PREFIX` variable.

## Potential downsides

N/A

## Impact on existing code

The proposal will affect only build system of sdl_atf.  
Changes may be required in Continuous Integration system configuration, scripts that use ATF, and manuals of ATF usage.

## Alternatives considered
Add extra scripts as workarounds for extending ATF functionality.
