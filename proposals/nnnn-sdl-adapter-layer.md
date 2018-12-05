# Specify SDL Adapter Layer

* Proposal: [SDL-NNNN](nnnn-oem-adapter-layer.md)
* Author: [Alexander Kutsan](https://github.com/LuxoftAKutsan)
* Status: **Awaiting review**
* Impacted Platforms: [Core / iOS / Android / Web / RPC / Protocol]

## Introduction

This proposal is about spliting SDL on **business logic** part, and **sdl adapter** part.

**SDL business logic** should contain platfrom agnostic code. All features of SDL implemented in that layer. 
**SDL adapter** should implement interfaces that SDL business logic will use for communication with user, platfrom, devices. 

## Motivation

Integration of SDL is complicated process. 
Integration of each new release of SDL requires big amount of additional resources and efforts.
Dependency managment in SDL project is compilcated and not obvious. 

Each OEM hardware have own approach for comunicaiton with mobile device.
Now entire SDL components should be replaced for porting SDL on specific OEM hardware.

Now for sdl contributors it is not obvious if some components should not contain platform specific code,
and should not contain business logic to avoid integration issues.

Now for sdl integrators it is not obvious if some components contains business logic or not,
and can be safety replaced by OEM implementation.

## Proposed solution

Extract platform specific parts, and parts that may be replaces for integration of SDL to OEM inrastructure to separate component - **SDL adapter**. 

### Architecture splitting of sdl parts :

Extract folowing components to SDL Adapter :

- Transport Adapters 
- HMI MessageBrocker
- SDL initialization

Big picture of SDL Adapter : 

![SDL Adapter](../assets/proposals/nnnn-sdl-adapter-layer/sdl_adapter.png) 


### Scope or responcubilities and interfaces of SDL business logic

**SDL business logic** component contains whole functionality of SDL :  

 - SDL protocol
 - Application management
 - Handling and processing RPCs
 - Resumption functionality
 - Policy functionality
 - State management

Folowing interfaces **SDL business logic** component should provide to SDL adapter : 
 
#### HMIMessageHandler

Interface used by **HMI Massage handler** to notify SDL about new HMI message. 

###### Methods : 

- `void OnMessageReceived (SPtr<HMIMessage>)` : Called by HMI Message Brocker when message from HMI received 
- `void OnErrorSending (SPtr<HMIMessage>)` :  Called by HMI Message Brocker if sending message to HMI failed 

### Scope of responcibilities or SDL adapter

### Open source SDL adapter


### Using modern CMake approach

SDL may use modern cmake approach for targets creation. It will simplify porting SDL to any platform : 

#### Use CMake name spaces 

Propose to use cmake with name spaces for all SDL components and dependencies. 
This best practice of cmake allow:
 - To make clear components dependencies;
 - To avoid dependency gaps (required for multi-threading compilation);
 - To keep components independent;
 - To understand components interface;
 - To unify external dependencies management.
 
Here are the drawbacks of the current structure of cmake usage:
1. Existing cmake structure does not allow easy and seamless integration to other operating systems.
2. Existing cmake structure has no unified management system of 3rd party libraries.
3. With existing cmake structure we have problems with components and libraries dependencies, and the modern approach should resolve it.
4. This new approach will make cmake files more clear and lightweight.


#### target_<link_libraries,include_directiries>

SDL CMake files should avoid using global cmake commands for adding compiler flags, include directories, linkage libraries, etc ...

This functions pollute the project compilation structure, adds hidden dependencies between components, and make cmake files unclear and confusing.

SDL CMake files should explicitly specify include directories, link libraries, compiler options for entire target that it compiles.

#### New Cmake approach detailed design 

##### SDL core repository structure:

 - `/CMakeLists.txt`: contains common build configs for all projects, include directories with some builds utils and helpers;
 - `/src/`: contains all sources of the project;
 - `/src/components`: contains sources of SDL Core components;
 - `/src/3rd_party`: contains sources of 3rd party components;
 - `/src/appMain`: contains sources of SDL Core main executable and config files for runtime;
 - `/src/docs/`: contains doxygen template for Software Detailed Design (SDD) document generation;
 - `/src/tools/`: contains tools for work with repository, helpers, formatters, git hooks, etc;
 - `/cmake`: contains additional cmake files with common code across components;
 - `/cmake/toolchains`: contains compilation toolchains for different platforms;
 - `/cmake/helpers`: contains cmake helpers with common code across components;
 - `/cmake/dependencies`: contains cmake file for finding certain dependency on the system.

Each folder should contain a README file with descriptions of contents and examples of usage if applicable.

##### 3rd party libraries management:

By default build system should not install to the system any additional libraries during compilation. 

If required version of certain dependency is available on the system, build system should use it.

If required version of certain dependency missed on the system, it build system should compile it and keep in `<build>` folder within `make` command.


**External dependencies** - dependencies that build system should download from official sources during cmake run.
**3rd party dependencies** - dependencies that build system should keep as sources in `src/3rd_party` directory.

SDL is responsible for 3rd party dependencies in the code and fixes that may also be applied to this code.

The list of SDL dependencies : 
  - boost : **external dependency**, if it was not found on the system, build system should download sources from official sources during `cmake` command run and compile within project during `make` command run.
  - libapr : **3rd party dependency**, if it was not found on the system, build system should take sources from `src/3rd_party/` and compile within project during `make` command run.
  - libaprutils : **3rd party dependency**, if it was not found on the system, build system should take sources from `src/3rd_party/` and compile within project during `make` command run.
  - liblog4cxx : **3rd party dependency**, if it was not found on the system, build system should take sources from `src/3rd_party/` and compile within project during `make` command run.
  - bson-clib : **external dependency**, if it was not found on the system, build system should download sources from official sources during `cmake` command run and compile within project during `make` command run.
  - json : **3rd party dependency**, if it was not found on the system, build system should take sources from `src/3rd_party/` and compile within project during `make` command run.

######  3rd party libraries installation rules

Compilation of libraries should not trigger their installation to the system by default.

Propose to use special CMAKE variable if user desires to install 3rd party libraries to the system: `THIRD_PARTY_INSTALL_PREFIX`.

If this variable is empty, SDL should install 3rd party and external dependencies libraries to `{BUILD_DIR}`/compile_dependencies

During `make install` SDL should copy all files required for SDL RUN to `{BUILD_DIR}`/bin, and libraries required for SDL RUN to `{BUILD_DIR}`/bin/lib.


## Potential downsides

## Impact on existing code

## Alternatives considered
