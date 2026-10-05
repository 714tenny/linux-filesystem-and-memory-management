# Documented filesystem and debugging commands

The screenshots show an equivalent workflow using the following commands. File names are course demonstration files.

```bash
sudo parted -l
ls -i wu.txt
ln -s wu.txt symlink_wu.txt
ln wu.txt hardlink_wu.txt
ls -l
```

The source also records GCC compilation and a GDB session with a breakpoint in `main` and inspection of variable/array values. The original C files are not supplied, so no new compilation or leak test is claimed here. Commands that set permissions should be chosen for each object's intended use rather than copied indiscriminately.
