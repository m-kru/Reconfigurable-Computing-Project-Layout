# Example Reconfigurable Computing Project Layout

## Overview

This is an example project layout for reconfigurable computing projects.

## Remarks

Try to group files by language, target or tool criteria.
Do **not** group files by modules.

For example, lets assume there are two modules `transmitter` and `receiver`.
Each of them has following associated files:
- .vhd - HDL description of the module,
- .xml - description of the module registers,
- .py - Python module control code used during prototype stage,
- .go - high performance module conrol code used in final implementation.
Do not.
