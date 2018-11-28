# Specify OEM SDL Adapter Layer

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

### Arhitecture splitting of sdl parts :

### Interface of SDL business logic

### Scope of responcibilities or SDL adapter

### Open source SDL adapter

### Using modern Cmake approach  

## Potential downsides

## Impact on existing code

## Alternatives considered
