# The Linux Kernel
[LinkedIn Learning](https://www.linkedin.com/learning/advanced-linux-the-linux-kernel)

---

- The Linux kernel is a program loaded up by a bootloader, such as GRUB
    - Typically has a name like `vmlinuz-<KERNEL VERSION>`
    - Takes command-line parameters
    - Kernel is an API
        - System calls
        - Virtual file system entries
        - Device drivers (via interacting with device files through system calls)
    - Kernel enforces privileges
        - Executes supervisor instructions (special CPU instructions that can only be executed by the kernel)
        - Implements security policies
        - Controls access to hardware and other resources
    - Kernels are modular
        - Only load code for installed hardware
        - Sufficient enough to boot to user space
- Kernel typicaly stored in `/boot`

---

## Surveying the Linux Kernel

#### Discover and control hardware
- Information about the hardware can be uncovered via commands like:
    - `lshw`: list hardware
    - `lspci`: *information* about PCI devices
    - `lsusb`: USB devices
    - `lsbk`: block devices
    - `lscpu`: the CPU
    - `lsdev`: other devices
- Can control the hardware with other commands:
    - `hdparm`: controlling hard disks
    - `proc`, `dev`, and `sys` filesystems: devices can be controlled via writing to certain device files
    - `inb` and `outb`: controlling I/O buffer devices, such as a keyboard
    - `setpci`: controlling PCI devices

#### Understand system call mechanics
- System calls are functions implemented by the kernel and meant to be called from user space
    - There are about 300 Linux system calls, and their declarations can usually be found in the
      `include/uapi/asm-generic/unistd.h` header
        - `man` section 2 also has information on the system calls
            - `man 2 read` would pull up information on the `read` syscall
- Applications don't directly call system calls, they call library functions that make the calls
    - C for example, will invoke syscalls via the `libc` library, and not directly
    - The library functions themselves use assembly to call the system calls and are therefore architecture-dependent
        - Usually involves putting parameters in registers and making the system call
    - Return values for system calls are negative if an error occurred
        - The error code is the absolute value of that number
        - If an error is returned, the library will set a global variable `errno` that represents the value of the error
          returned (the absolute value), and the library function will return the result of the system call
            - So if an error occurred, and the system call returned -1, `errno` will be set to 1 while the library return
              value will be -1
            - **If there is no error, `errno` will not be set**
- `strace` can be used to track the system calls a process is making
    - `strace -c <PROGRAM>` reports how many times some program is calling some system call

#### Read messages from the kernel
- `printk` is the kernel's way to print messages, similar to `printf`
    - The output is sent to a buffer in RAM and important ones are printed on the system console
        - Which `printk` messages that are allowed to print is configurable
    - Typically some logging daemon is spinning and capturing kernel log messages
        - `/var/log/messages` is a common location for the log output
    - `dmesg` shows RAM buffer contents
        - This buffer is limited in size so not all messages will be displayed
- The current kernel version can be output with the command `uname -r`

#### Using the `/proc` and `/sys` filesystems
- `proc` and `sys` are virtual filesystems
- Their contents are not stored on disk; rather the contents for device files are generated whenever a command to
  read/write to them is executed; each file and directory has an associated function in the kernel to produce these
  contents on demand
- `proc` contains process information as well as other things
    - A lot of kernel information is stored here as well
        - `/proc/meminfo` contains the kernel's memory usage, how much is available, etc
    - the `sys` subdirectory contains tunable variables that can be changed (`/proc/sys` is different from `/sys`)
        - Writing to these "files" will overwrite the current value
        - There is also the `sysctl` command which allows you to set these options
            - `sysctl -a` will list all the kernel variables that can be tuned
            - `sysctl <VARIABLE NAME>=<VALUE>` will set an option to a value, ex.
              `sysctl net.ipv4.ip_forward=0`
    - Each process has a directory identified by its PID
        - Contains info on its memory, what program is running, what files it has open, etc
            - The symlink `exe` points to the file that's currently running in that process, for example
            - The symlink `cwe` is its current working directory
            - `cmdline` is how the program was run
            - The `fd` directory contains file descriptors owned by that process
                - The contents of this directory might be something like so:
                ```
                lrwx--- ... 0 -> /dev/pts/1
                lrwx--- ... 1 -> /dev/pts/1
                lrwx--- ... 2 -> /dev/pts/1
                ```

                0, 1, and 2 are file descriptors for stdin, stdout, and stderr, respectively. `/dev/pts/1` refers to some
                TTY instance; running `tty` in a terminal will reveal which device it's currently using
        - Thread info is under the `task` subdirectory
- `sys` is where the `sysfs` is mounted at boot
    - `sysfs` stores kernel object info: hardware info for example
    - Device files are stored in `/sys/dev` and are used for character drivers and block drivers
        - Device files have a major number, a minor number, and a type ("c" for character, "b" for block)
            - A driver can have multiple device files available
            - Typically the major number identifies the driver, and the minor number represents some subset of
              functionality that makes sense to that driver
                - A serial port driver may have one major number for itself then several minor number device files for each
                  port, for example. A hard disk may have minor numbers for different partitions
        - Performing `ls -l` on a device file produces output like
        ```
        crw- rw- rw . 1 root root 1, 3 Apr 5 12:07 /dev/null
        ```
        the "c" at the beginning indicates it's a character type device, and the `1, 3` indicates the major number and
        the minor number, respectively

        - Device drivers will overload system calls so that applications in user space can interact with them
            - A character device, for example, can implement its own `open()`, `read()`, `write()`, and `ioctl()`
                - `open` will return a file descriptor that can be used to interact with the device via device file
        - A process opens a device file and can then read/write with its file descriptor. The kernel will call that
          device driver's functions behind the scenes
            - Ex. `echo "foo" > /dev/null`: the kernel will call the `null` device's `open` and `write` function
              behind the scenes
                - The driver for `/dev/null` also implements device files for `/dev/0`, etc; their minor numbers are different
                  however, so their functionality isn't guaranteed to be the the same
    - `/sys/block` contains the block devices that are currently available to the kernel
        - This is where hard disks can be found
            - Their partitions can be located inside their respective directories; ex. `sys/block/sda/sda1`,
              `sys/block/sda/sda2`

~~~~~~~~~~~~~~~~~~~~~~~~~OTHER NOTES GO HERE~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~OTHER NOTES GO HERE~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~OTHER NOTES GO HERE~~~~~~~~~~~~~~~~~~~~~~~~~

---

## Working with Loadable Kernel Modules

#### Introduction to the loadable kernel modules (LKMs)
- Kernel modules are identified with the `.ko` extension (kernel objects)
- LKMs contain code to run in kernel space
- Dynamically adds functionality to a running kernel; drivers are an example of LKMs, but the concept of LKMs is a lot broader
  than just device drivers
- LKMs are typically written in C and compiled for a particular kernel version - it will not be compatible for other kernel
  versions

| Advantages |
|------------|
| Can be a relatively minimal kernel |
| Add functionality without rebuilding or rebooting |
| Allows for only the needed functionality to be loaded |
| Live updates |
| Accelerated development |

| Disadvantages |
|---------------|
| Kernel modules shouldn't be unloaded once they're loaded |
| Kernel modules must be built specifically for your current kernel version and kernel configuration |
| Modules run with kernel permissions |

#### Find the LKMs
- LKMs are installed in `/lib/modules` with each installed kernel version having its own subdirectory
    - The modules are organized in different subdirectories under the kernel version
        - Each module needs to have its own unique name
    - There are also config files

#### Using LKM commands
- `lsmod` lists the currently loaded modules, chronologically. There are four columns output:

|||
|---|---|
| **Module** | name of the loaded module |
| **Size** | size of the module, in bytes |
| *unnamed* | module count; how many times the kernel thinks that module is in use |
| **Used by** | shows dependencies between modules; some modules may depend on other loaded modules |

- `modprobe` loads a module
    - Loads any dependencies that the module has as well
    - Only looks in the `/lib/modules` directory for `.ko` files
    - Can remove modules with `modprobe -r`, which will also remove dependencies if they're no longer needed
- `rmmod` command removes a module
    - Kernel will prevent you from removing a module that is in use
    - `rmmod -f` lets you forcibly remove a module, even if it's currently in use
    - Won't remove modules that depend on the one that is being removed
- `modinfo` retrieves module info, such as the author, its parameters, aliases, etc
    - The "vermagic" string of a module must match the "vermagic" string of the kernel; otherwise they are
      considered incompatible
- `depmod` generates module config files that `modprobe` will use
    - Creates a matrix of dependencies for modules; check the `/lib/modules/<kernel ver>/modules.dep`
      for a list of these dependencies for all kernel modules that `depmod` creates
    - Typically this command is run automatically when a module is installed
- `insmod` will load modules as well, but will not load any dependencies
    - Loads a module from a given path, so modules can be loaded from locations other than `/lib/modules`
    - `modprobe` is the preferred way to load modules
    - This command will not return until the loaded module's initialization function returns
        - Function may return an error message

#### Identify how LKMs work
- Kernel modules are built using kernel makefiles

```bash
make -C /lib/modules/$(uname -r)/build M=$PWD modules
# -C tells make to use the kernel makefile in our kernel's directory
# M= is a macro used by the kernel makefile that points to the modules to build
# make target is "modules"
```

- The kernel makefile will look for a makefile in this directory for the module it's going to be building
    - This makefile must have commands assigned to the special target `obj-m`

```makefile
// Given a directory that contains the mod.c source file, the makefile should look something like:
obj-m := mod.o
```

This will make `mod.c -> mod.o -> mod.ko`.

- Kernel module code has certain unique features:
```c
//These are includes from the kernel source tree, and are different than the ones that would be made inside
//  of userspace code.
#include <linux/init.h>
#include <linux/module.h>
#include <linux/sched.h>

int my_init( void )
{
    printk( "foo\n" );
    //... do something
    return 0;
}

void my_cleanup( void )
{
    //... do something
}

// Register the init and exit functions that we created using special macros
module_init( my_init );
module_exit( my_cleanup );
```

---

## Examining Linux Kernel Source

#### Get the kernel source
- May have to pull the source via a package manager ( `yumdownloader -source kernel` ), via Git, etc
    - The official kernel can be found at [www.kernel.org](kernel.org)
    - If an RPM is downloaded, it can be installed via these steps:
        1. `rpm -i kernel*.rpm` should create an `rpmbuild` in the home directory
        2. `cd ~/rpmbuild/SPECS` to get `kernel.spec` file, which contains directions on building the RPM
        3. `rpmbuild -bp kernel.spec`
            - `-bp` stands for "build prep" and will prepare to build the kernel itself, extracting the tarball
              and applying any patches if necessary
        4. `cd ~/rpmbuild/BUILD` to find the source code
        5. Use the kernel Makefile located here to perform whatever operation you like
            - There should be a kernel Makefile in this directory; use `make help` to see what options there are
            - There should be options like `make menuconfig` or `make xconfig` that provide a graphiv interface
              for selecting what kernel options you'd like to have
              - Configuration parameters are stored in `.config`
            - Other options typically include:
                - `make bzImage` to create a bootable kernel in the bz image format (the common format for
                  x86 and x86_64 architectures)
                - `make modules` will compile the modules
                - `make install_modules` will install modules in the `/lib/modules` directory
                - `make install` will install the kernel
                - `make clean` will remove object files

#### Explore the kernel Makefile
- The beginning of the kernel Makefile contains version information that looks something like:
```
VERSION = 3
PATCHLEVEL = 10
SUBLEVEL = 0
EXTRAVERSION =
NAME = Some Name
```

These fields shouldn't be modified, with the exception of EXTRAVERSION that can be used if you want to build
your own kernel without overwriting an existing version
- Other Makefile targets:
    - `make mrproper` cleans even more than `make clean`
        - Removes generated files, config files, and backup files
    - `make distclean` is the most rigorous cleaning command
        - Does everything `mrproper` does, as well as removes editor backup and patch files
    - `make xconfig` provides a QT-based frontend for configuring kernel options
    - `make gconfig` provides a GTK-based frontend for configuring kernel options
    - `make menuconfig` is a TUI-based frontend for configuring kernel options, useful if there is no window
      server running
    - `make config` is a CMD-based frontend for configuring kernel options
        - Prompts for input for almost every single kernel option available, which number in the thousands