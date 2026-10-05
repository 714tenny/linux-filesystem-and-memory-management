# Linux Filesystem, Permissions & Memory Management — Investigation Notes

## Objective

Explore Linux filesystem structures and memory-management mistakes in C. The source contains partition output, link commands, file listings, debugger output, compiler warnings, and written explanations.

## Filesystem investigation

`parted -l` showed a VMware virtual disk using GPT with a small `bios_grub` partition and an ext4 partition. The source measured about 1031–1032 kB of unallocated space around the partitions. A filesystem format does not by itself establish which directory is mounted there.

The original file's inode number was recorded as `1180583`. A symbolic link called `symlink_wu.txt` pointed to `wu.txt`; a hard link called `hardlink_wu.txt` was created with `ln`.

| Concept | Interpretation |
| --- | --- |
| Inode | Filesystem object metadata; names are directory entries referring to objects |
| Symbolic link | Separate object containing a path to another object |
| Hard link | Another directory entry referring to the same inode within a filesystem |
| `755` | Owner read/write/execute; group/others read/execute |
| `644` | Owner read/write; group/others read |

The screenshot's listing includes permissive executable bits, including `777`-style entries. This is not presented as a validated least-privilege configuration. The report discussed `755` and `644`, but discussion alone does not prove those modes were applied. On normal Linux systems, a symlink's displayed mode is not the primary access control on its target; target permissions remain relevant.

## Memory investigation

The written report discusses an uninitialized variable, out-of-bounds array access at `array[6]`, and allocation with `malloc` without a corresponding `free`. GDB was used to stop in `main` and inspect values; another screenshot shows compiler warnings involving `printf` and the missing declaration/header. A later screenshot displays an `Uninitialized Variable` output, so the issue cannot be considered fully remediated solely from that run.

| Issue | Correct interpretation | Proposed validation |
| --- | --- | --- |
| Uninitialized variable | Indeterminate value; unsafe reads may have undefined behavior | Initialize values and inspect compiler/sanitizer findings |
| Array index outside bounds | Undefined behavior; crash is not guaranteed | Check valid index range and use an address sanitizer |
| Allocation without release | Resource leak during execution | Pair owned allocations with `free` and perform leak analysis |
| Missing `printf` declaration | Compiler diagnosed missing/inconsistent declaration | Include the correct header and rebuild with warnings |

The OS normally reclaims a process's resources at exit; the leak concern is unreleased memory while the program runs. Ordinary GDB stepping and a successful run do not independently certify the absence of leaks. No Valgrind, sanitizer, or leak-free output is supplied here, so the project does not claim a verified complete fix.

## Lessons and scope

Permissions and link behavior are useful for Linux support and incident analysis. Debugger values illustrate program state, while compiler and memory-analysis tools provide complementary checks. The original C source is not available as a separate file and has not been reconstructed or invented for this publication.

## Suggested follow-up

Use explicit ownership and bounds checks, enable meaningful compiler warnings, and run a memory checker against the actual original program. These are proposed improvements rather than executed tests. No new VM or code execution was performed while preparing this repository.
