# LPIC-1.102: Linux Installation and Package Management

## Lesson 102.3 Manage shared libraries

In this lesson we will be discussing shared libraries, also known as shared objects: pieces of compiled, reusable code like functions or classes, that are recurrently used by various programs.

To start with, we will explain what shared libraries are, how to identify them and where they are found. Next, we will go into how to configure their storage locations. Finally, we will show how to search for the shared libraries on which a particular program depends.

### Concept of Shared Libraries
Similar to their physical counterparts, software libraries are collections of code that are intended to be used by many different programs; just as physical libraries keep books and other resources to be used by many different people.

To build an executable file from a program’s source code, two important steps are necessary. First, the compiler turns the source code into machine code that is stored in so-called object files. Secondly, the linker combines the object files and links them to libraries in order to generate the final executable file. This linking can be done statically or dynamically. Depending on which method we go for, we will be talking about static libraries or, in case of dynamic linking, about shared libraries. Let us explain their differences.

`Static libraries`
- A static library is merged with the program at link time. A copy of the library code is embedded into the program and becomes part of it. Thus, the program has no dependencies on the library at run time because the program already contains the libraries code. Having no dependencies can be seen as an advantage since you do not have to worry about making sure the used libraries are always available. On the downside, statically linked programs are heavier.

`Shared (or dynamic) libraries`
- In the case of shared libraries, the linker simply takes care that the program references libraries correctly. The linker does, however, not copy any library code into the program file. At run time, though, the shared library must be available to satisfy the program’s dependencies. This is an economical approach to managing system resources as it helps reduce the size of program files and only one copy of the library is loaded in memory, even when it is used by multiple programs.

### Shared Object File Naming Conventions
The name of a shared library, also known as soname, follows a pattern which is made up of three elements:

- Library name (normally prefixed by `lib`)
- `so` (which stands for “shared object”)
- Version number of the library

Here an example: `libpthread.so.0`

By contrast, static library names end in `.a`, e.g. `libpthread.a`.

```
Note
Because the files containing shared libraries must be available when the program is executed, most Linux systems contain shared libraries. Since static libraries are only required in a dedicated file when a program is linked, they might not be present on an end user system.
```

`glibc` (GNU C library) is a good example of a shared library. On a Debian GNU/Linux 9.9 system, its file is named `libc.so.6`. Such rather general file names are normally symbolic links that point to the actual file containing a library, whose name contains the exact version number. In case of `glibc`, this symbolic link looks like this:

```
$ ls -l /lib/x86_64-linux-gnu/libc.so.6
lrwxrwxrwx 1 root root 12 feb  6 22:17 /lib/x86_64-linux-gnu/libc.so.6 -> libc-2.24.so
```

This pattern of referencing shared library files named by a specific version by more general file names is common practice.

Other examples of shared libraries include `libreadline` (which allows users to edit command lines as they are typed in and includes support for both Emacs and vi editing modes), `libcrypt` (which contains functions related to encryption, hashing, and encoding), or `libcurl` (which is a multiprotocol file transfer library).

Common locations for shared libraries in a Linux system are:

- `/lib`
- `/lib32`
- `/lib64`
- `/usr/lib`
- `/usr/local/lib`

```
Note
The concept of shared libraries is not exclusive to Linux. In Windows, for example, they are called DLL which stands for dynamic linked libraries.
```

### Configuration of Shared Library Paths
The references contained in dynamically linked programs are resolved by the dynamic linker (`ld.so` or `ld-linux.so`) when the program is run. The dynamic linker searches for libraries in a number of directories. These directories are specified by the library path. The library path is configured in the `/etc` directory, namely in the file `/etc/ld.so.conf` and, more common nowadays, in files residing in the `/etc/ld.so.conf.d` directory. Normally, the former includes just a single `include` line for `*.conf` files in the latter:

```
$ cat /etc/ld.so.conf
include /etc/ld.so.conf.d/*.conf
```

The `/etc/ld.so.conf.d` directory contains `*.conf` files:

```
$ ls /etc/ld.so.conf.d/
libc.conf  x86_64-linux-gnu.conf
```

These `*.conf` files must include the absolute paths to the shared library directories:

```
$ cat /etc/ld.so.conf.d/x86_64-linux-gnu.conf
# Multiarch support
/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu
```

The `ldconfig` command takes care of reading these config files, creating the aforementioned set of symbolic links that help to locate the individual libraries and finally of updating the cache file `/etc/ld.so.cache`. Thus, `ldconfig` must be run every time configuration files are added or updated.

Useful options for `ldconfig` are:

`-v, --verbose`
- Display the library version numbers, the name of each directory, and the links that are created:

```
$ sudo ldconfig -v
/usr/local/lib:
/lib/x86_64-linux-gnu:
    libnss_myhostname.so.2 -> libnss_myhostname.so.2
	libfuse.so.2 -> libfuse.so.2.9.7
	libidn.so.11 -> libidn.so.11.6.16
	libnss_mdns4.so.2 -> libnss_mdns4.so.2
	libparted.so.2 -> libparted.so.2.0.1
	(...)
```

So we can see, for example, how `libfuse.so.2` is linked to the actual shared object file `libfuse.so.2.9.7`.

`-p, --print-cache`
- Print the lists of directories and candidate libraries stored in the current cache:

```
$ sudo ldconfig -p
1094 libs found in the cache `/etc/ld.so.cache'
    libzvbi.so.0 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libzvbi.so.0
	libzvbi-chains.so.0 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libzvbi-chains.so.0
	libzmq.so.5 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libzmq.so.5
	libzeitgeist-2.0.so.0 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libzeitgeist-2.0.so.0
	(...)
```

Note how the cache uses the fully qualified soname of the links:

```
$ sudo ldconfig -p |grep libfuse
	  libfuse.so.2 (libc6,x86-64) => /lib/x86_64-linux-gnu/libfuse.so.2
```

If we long list `/lib/x86_64-linux-gnu/libfuse.so.2`, we will find the reference to the actual shared object file `libfuse.so.2.9.7` which is stored in the same directory:

```
$ ls -l /lib/x86_64-linux-gnu/libfuse.so.2
lrwxrwxrwx 1 root root 16 Aug 21  2018 /lib/x86_64-linux-gnu/libfuse.so.2 -> libfuse.so.2.9.7
```

```
Note
Since it requires write access to /etc/ld.so.cache (owned by root), you must either be root or use sudo to invoke ldconfig. For more information about ldconfig switches, refer to its manual page.
```

In addition to the configuration files described above, the `LD_LIBRARY_PATH` environment variable can be used to add new paths for shared libraries temporarily. It is made up of a colon-separated (`:`) set of directories where libraries are looked up. To add, for example, `/usr/local/mylib` to the library path in the current shell session, you could type:

```
$ LD_LIBRARY_PATH=/usr/local/mylib
```

Now you can check its value:

```
$ echo $LD_LIBRARY_PATH
/usr/local/mylib
```

To add `/usr/local/mylib` to the library path in the current shell session and have it exported to all child processes spawned from that shell, you would type:

```
$ export LD_LIBRARY_PATH=/usr/local/mylib
```

To remove the `LD_LIBRARY_PATH` environment variable, just type:

```
$ unset LD_LIBRARY_PATH
```

To make the changes permanent, you can write the line

```
export LD_LIBRARY_PATH=/usr/local/mylib
```

in one of Bash’s initialization scripts such as `/etc/bash.bashrc` or `~/.bashrc`.

```
Note
LD_LIBRARY_PATH is to shared libraries what PATH is to executables. For more information about environment variables and shell configuration, refer to the respective lessons.
```

### Searching for the Dependencies of a Particular Executable
To look up the shared libraries required by a specific program, use the `ldd` command followed by the absolute path to the program. The output shows the path of the shared library file as well as the hexadecimal memory address at which it is loaded:

```
$ ldd /usr/bin/git
    linux-vdso.so.1 =>  (0x00007ffcbb310000)
	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f18241eb000)
	libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f1823fd1000)
	libresolv.so.2 => /lib/x86_64-linux-gnu/libresolv.so.2 (0x00007f1823db6000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f1823b99000)
	librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007f1823991000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f18235c7000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f182445b000)
```

Likewise, we use `ldd` to search for the dependencies of a shared object:

```
$ ldd /lib/x86_64-linux-gnu/libc.so.6
	/lib64/ld-linux-x86-64.so.2 (0x00007fbfed578000)
	linux-vdso.so.1 (0x00007fffb7bf5000)
```

With the `-u` (or `--unused`) option `ldd` prints the unused direct dependencies (if they exist):

```
$ ldd -u /usr/bin/git
Unused direct dependencies:
	/lib/x86_64-linux-gnu/libz.so.1
	/lib/x86_64-linux-gnu/libpthread.so.0
	/lib/x86_64-linux-gnu/librt.so.1
```

The reason for unused dependencies is related to the options used by the linker when building the binary. Although the program does not need an unused library, it was still linked and labelled as `NEEDED` in the information about the object file. You can investigate this using commands such as `readelf` or `objdump`, which you will soon use in the explorational exercise.

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)