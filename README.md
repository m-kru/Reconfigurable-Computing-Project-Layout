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

### FuseSoc `.core` files

If you use [FuseSoc](https://github.com/olofk/fusesoc) in your project then:
1. Use 2 spaces for indentation in `.core` files.
This is enough to make them readable.
However, what is more important, this will make your file look coherent and uniform, even in case of dictionaries as list items.
This is becase `- ` (hyphen + space) occupies the same space as 2 space characters.
2. Put `.core` files as close to the related HDL files as possible.
The recommended way is to make separate directory for each distinct core and put there all HDL files and `.core` file.
HDL filse might be further grouped into subdirectories.
However, such approach is not always handy or even possible.
Sometimes project depends on an external library, that does not have `.core` files at all.
In such case make separate directory solely for `.core` files.

### Is it a config or is it a script?

Sometimes, especially in case of `.tcl` files, it is not clear whether some file is a configuration file or maybe a script file.
For example, following `.tcl` file is used for setting Vivado project properties.
```tcl
set_property target_language VHDL [current_project]
set_property simulator_language Mixed [current_project]

set_property flow "Vivado Synthesis 2020" [get_runs synth_1]
set_property strategy Flow_PerfOptimized_High [get_runs synth_1]

set_property flow "Vivado Implementation 2020" [get_runs impl_1]
set_property strategy Performance_Retiming [get_runs impl_1]
```
From formal point of view this is a Tcl script.
However, from a logical point of view it sets the Vivado project configurations.
As such it is a configuration file.
Ask yourself a question "What is the main goal of this particular file?"
If the answer is "setting some configuration", then put it into the `/configs` directory.
If the answer is "some action other than setting some configuration", then put it into the `/scripts` directory.
If the answer is both, then split it into 2 (or more) files and put them into relevant directories.
If the file can't be split, then make whatever decision you feel is right.

### Symbolic links

Do not be afraid of using symbolic links.
This is very simple and powerful concept.
Use symbolic links to share common files between software "subprojects" or to share common stuff between projects in case of monorepo.

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

Files that are frequently automatically generated and are later used during build or run stage.
Do **not** put files that are generated once (or rarely) here.
For example, if one generates pinout constraint files using tools such as [fp2p](https://github.com/m-kru/fp2p), then one should put them into the `/constr` directory.
Whether to store this directory or certain files in the git is up to the user.

### `/configs`

Configuration files.

Configuration files, configuration file templates, default configurations etc.

### `/constr`

Project related design constraint files.

Good example are constraint files related with pinout or constraints related with PLLs safe clock startup.
If convenient use relevant subdirectories for constraint files grouping.
Do **not** put constraint files that are strictly associated with any particular module (aka. scoped to ref) here.

### `/docs`

Design and user documents.

Project documentation as well as third party data sheets etc.

### `/ip`

Intellectual Property files.

Make separate subdirectory for each core.
Sometimes there is a need to move a legacy IP from older version of an EDA tool.
In such case put it into `/legacy` subdirectory.

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

## Software Directories

Name the software directories based on the primary language name.
For example, `/c`, `/go`, `/python`.
Within these directories obey the patterns/rules specific for given language.
In other words, treat them as logically separate software projects.

It might happen that these software "subprojects" need to share some files.
In such case keep those common files somewhere in the projects root path.
Access them using relevant paths or use symbolic links.

## Directories You Shouldn't Have

### `/build`

Reserve this directory for build stuff.
Even if your built system does not use this directory, do not store any files in it and do not add it to git.
Some build systems or tools use this directory by default.
If you switch build system or add one of such tools, you might get into problems.
This can be easily avoided by simply reserving it for build output only.

### `/src`

This name is too confusing to be in project's root path.
In reconfigurable computing project there are always sources for at least 2 programming languages or hardware description languages.
As mentioned in the remarks section, files should not be grouped by modules.
In such case `/src` does not indicate what source files are placed there.

However, `/src` directory can be used in subdirectories.
For example in directory containing C source files or HDL modules directories.

## Feedback

If you have any questions, or you know how to organize something better, do not hesitate to submit an issue.
