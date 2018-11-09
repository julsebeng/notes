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