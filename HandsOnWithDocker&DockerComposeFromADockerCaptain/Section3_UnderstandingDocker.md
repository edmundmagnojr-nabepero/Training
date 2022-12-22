# Section 3: Understanding Docker

### Introduction

### Virtual Machines vs Docker
+ Running a VM on a server - start in minutes, wastes resources, isolate systems
  Infrastructure - laptop
  Host Operating System
  Hypervisor
  Guest OS
  Bins/Libs
  Apps
+ Docker Containers - starts in milliseconds, saves resources, isolate apps
  Infrastructure
  Host Operating System
  Docker Daemon
  Bins/Libs
  Apps
+ Analogy

Virtual Machines | Docker Containers
--- | ---
Houses | Apartments
Fully self contained | Share infrastructure
| | Maximize your size needs

_**Docker Containers**_ share resources with host operating system through docker daemon whereas _**Virtual Machines**_ do not.
### VMs vs Docker Containers in the Real World
+ VMs
  Web hosting company
  Testing entire systems
  Ubuntu in production
  isolates systems
+ Docker
  isolates applications

### Visualizing Docker's Architecture
