# sdccrm

## What is sdccrm?
sdccrm is a dead code optimization tool for the stm8 port of sdcc that removes unused functions. By configuring an entry point, sdccrm creates a function call tree and determines which labels can be optimized away.

## Usage
```bash
sdccrm [options] file1 file2 ...
```
sdccrm will output .asmrm files unless the -r switch is used, which replaces the original .asm files:
```bash
sdccrm -r file1 file2 ...
```

Symbols that are not called from the generated function call tree (e.g.: interrupt handlers only referrenced on the interrupt vector) can be explicitely defined by the user. For example:

```bash
sdccrm -x _do_not_exlude_this_label file1 file2 ...
```
_main is defined as the default entry label. However, a user-defined entry label can be defined:

```bash
sdccrm -e _new_entry_point file1 file2 ...
```

A verbose mode can be enabled by using the -v switch, which informs about what is being optimized away:

```bash
sdccrm -v file1 file2 ...
```
## Why this tool?
Unfortunately, as of sdcc-3.9.0, unused functions are not removed by the optimizer. After reading its source code thoroughly and being under time pressure, it seemed like a good idea to implement a separate tool for this.

## How does it work?
sdccrm takes one or more .asm files, which are usually generated by sdcc when using the -S (assemble, no compile) switch. Then, it takes a user-defined entry point ("_main" is assumed by default unless indicated otherwise) and generates a function call tree. All unused labels are removed away from the .asm file so sdcc can now compile these optimized .asm files seamlessly, while optimizing MCU memory usage.

## Why is this optimization not done by the compiler?
Sincerely, I have no idea - I guess sdcc being essentially an open-source, community-driven effort might lack some more development. However, I needed this optimization as I was partly using a third-part API which included many functions that I did not need. I just cannot find acceptable these unused functions are not optimzed away, as it took more than half of the microcontroller memory.

# License
Similarly to sdcc, sdccrm is licensed under GPLv3.

# Known issues
Removing unused declarations and definitions will surely give problems when --debug switch is used by sdcc, as debugging symbols need to point to those now-unexisting labels.
Unused static variables are not being removed.
