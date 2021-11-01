# Example Reconfigurable Computing Project Layout

## Overview

This is an example project layout for reconfigurable computing projects.

## Remarks

### The "firmware" word

Try to avoid the "firmware" word.
It is not clear what is meant by the word "firmware" in a reconfigurable computing project.
Is it a software running on a processor synthesized into the FPGA logic?
Is it a software running on a SoC CPU?
Is it a HDL?
Is it a bitstream programmed into the FPGA?
Is it all of them?

It has been already suggested in the [Standard Computer Dictionary, IEEE Std 610, 1990](https://ieeexplore.ieee.org/document/182763), that the "firmware" term is confusing.
> The confusion surrounding this term has led some to suggest that it be avoided altogether.

### Files grouping

Try to group files by language, target or tool criteria.
Do **not** group files by modules.

For example, lets assume there are two modules `transmitter` and `receiver`.
Each of them has following associated files:
- `.vhd` - HDL description of the module,
- `.xml` - description of the module registers,
- `.py` - Python module control code used during prototype stage,
- `.go` - high performance module conrol code used in final implementation.

Do not put all these files into single directory named `transmitter` or `receiver` respectively (location of such directory within the repository is irrelevant).
This feels very tempting and gives an impression of the proper encapsulation.
However, this is false impression.
Such approach has many drawbacks and leads to numerous complications.
Just to name a few:
- A need for hacks and workarounds for software module/packages creation.
- No out of hand IDEs support.
- Longer, more complex scripts for artifacts collection in CI/CD.

The reason for this is quite simple.

So, instead of following layout:
```
my_project/
├── receiver
│   ├── receiver.go
│   ├── receiver.py
│   ├── receiver.vhd
│   └── receiver.xml
└── transmitter
    ├── transmitter.go
    ├── transmitter.py
    ├── transmitter.vhd
    └── transmitter.xml
```
apply one of below layouts:
```
my_project/
├── go
│   └── pkg
│       └── my_project
│           ├── receiver.go
│           └── transmitter.go
├── hdl
│   ├── receiver
│   │   └── receiver.vhd
│   └── transmitter
│       └── transmitter.vhd
├── python
│   ├── receiver.py
│   └── transmitter.py
└── register_tool
    ├── receiver.xml
    └── transmitter.xml
```
```
my_project/
├── go
│   └── pkg
│       └── my_project
│           └── my_project.go
├── hdl
│   ├── receiver
│   │   └── receiver.vhd
│   └── transmitter
│       └── transmitter.vhd
├── python
│   └── my_project
│       ├── __init__.py
│       ├── receiver.py
│       └── transmitter.py
└── register_tool
    └── my_project.xml
```
Of course there are more correct ways.
However, the rule is simple.
Obey the standard ways of the programming languages and tools.

## Directories

### `/autogen`

Automatically generated files.

Automatically generated files that are later used during build or run stage.
Whether to store this directory or certain files in the git is up to the user.

### `/configs`

Configuration files.

Configuration files, configuration file templates, default configurations etc.

### `/constr`

Project related design constraint files.

If convenient use relevant subdirectories for constraint files grouping.
Do **not** put constraint files that are strictly associated with any particular module (aka. scoped to ref) here.

### `/docs`

Design and user documents.

Project documentation as well as third party data sheets etc.

### `/ip`

Intellectual Property files.

Make separate subdirectory for each core.

### `/scripts`

Scripts to perform various analysis, build, install, etc operations.

These scripts should keep the root level Makefile small and simple.
If project grows, and more and more scripts are added, one can group scripts into relevant subdirectories.
For example, put `.tcl` scripts into `/tcl` subdirectory.

### `/submodules`

Git submodules.

Do not use any different name for directory containing git submodules.
With `submodules` name it is clear that the directory contains submodules.
One does not even have to `cd` into the directory to check what is inside.

### `/tests`

All stuff related with tests that does not match to any other directories.

Feel free to structure the `/tests` directory anyway you want.
