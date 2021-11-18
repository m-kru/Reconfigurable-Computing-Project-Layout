# `/hdl`

Files related with cores implemented in Hardware Description Languages.

Within the directory create subdirectories for distinct cores.
If you use FuseSoc, then place appropriate `.core` files in the root of subdirectories.
The core subdirectory must contain not only the hardware description of the core, but also all files closely related with the core.
For example:
- constraint files (aka. scoped to ref constraints),
- memory initialization files (`.mif`),
- `.tcl` scripts,
- FuseSoc `.core` files.

Feel free to structure the core subdirectories anyway you want.
